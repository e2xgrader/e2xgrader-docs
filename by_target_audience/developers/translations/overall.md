### Prerequisites

#### gettext

In addition to a running development setup for JupyterLab extensions, the gettext software is required. Most linux-distros 
include gettext by default, but sometimes it needs to be obtained manually. To find out if you have gettext, just try to
run `xgettext` from your shell. If it complains, that no input file was given, it is already installed.

#### jupyterlab-translate

The tool `jupyterlab-translate` will be used to extract, convert and bundle translation files. It can be installed via `pip` or `conda`:

```shell
pip install jupyterlab-translate

# alternative:
conda install jupyterlab-translate -c conda-forge
```

### What do I need to do to add translations to my JupyterLab extension?

> [!NOTE]
> Steps 1-4 also apply when changing existing translations. The commands specified, will ammend the translation files
> while preserving the existing translations.

> [!NOTE]
> In the following guide, angle brackets are used to represent placeholders. For example `<my_extension>` should be replaced
> with the name of your extension.

#### 0. Preparation - Configure your extension to include translation files

Add the following lines to your `pyproject.toml`:
```toml
[project.entry-points."jupyterlab.locale"]
<my_extension> = "<my_extension>"
```

Your `pyproject.toml` will already have the definition for a `sdists` buid-target. Ammend its `artifacts`-definition to include
the `.json` and `.mo` language-files:
```toml
[tool.hatch.build.targets.sdist]
artifacts = [
    "<my_extension>/labextension", # this line was already present
    "<my_extension>/locale/**/*.json", # this line was added
    "<my_extension>/locale/**/*.mo" # this line was added
]
exclude = [".github", "binder"]
```


#### 1. Mark strings in code for translation

All strings in the source, that should be translated, must be marked. How this is done, differs between Python and TypeScript:
- [Preparing TypeScript files for translation](./typescript.md)
- [Preparing Python files for translation](./python.md)

#### 2. Prepare translation files

Firstly, all marked strings must be exported. This is done by running the following command. Subsequently, a file with the
same name as your extension, that is suffixed with `.pot`, will be created. This file must not be changed manually.

```shell
jupyterlab-translate extract <jlab_extension_dir> <my_extension>

#example:
jupyterlab-translate extract ./ my_extension
```

After the original strings where extracted, translation files for all the desired target languages must be created. This
can be done by running the following command for each language separately.

```shell
jupyterlab-translate update <jlab_extension_dir> <extension> -l <LOCALES>

#example:
jupyterlab-translate update ./ my_extension -l de_DE
```

#### 3. Translate extracted strings

You may now add translations for the extracted strings. This can be done by editing the `.po` files located in the respective
folders of each target language (`/<my_extension>/locale/<target_locale>/LC_MESSAGES/<my_extension>.po`). For each extracted
string (`msgid`) a translation (`msgstr`) may be specified. If you leave a `msgstr` field empty the original string will
be used instead.

#### 4. Bundle translations with the extension package

Lastly, the translations must be converted to JSON. This can be done using the following command:

```shell
jupyterlab-translate compile <jlab_extension_dir> <my_extension>

#example:
jupyterlab-translate compile ./ my_extension -l <LOCALES>
```

Proceed by building your extension. You may test if the translations are present, after you have reinstalled you extension.

> [!IMPORTANT]
> Translations won't be updated if you use just `jlpm watch` or `jlpm build` during development. The whole python-package
> must be built (`python -m build`) and reinstalled manually, for changes in translations to take effect.

### Where are my translation files located? / Where will my translation files be located?

All translation files are located inside the `locale` directory of the python-package of your extension, by default. This
directory has commonly the same name as your extension.

```
    /
    ├── <my_extension>/
    │   └── locale/
    │       ├── de_DE/
    │       │   └── LC_MESSAGES/
    │       │       ├── <my_extension>.json
    │       │       ├── <my_extension>.mo
    │       │       └── <my_extension>.po
    │       └── <my_extension>.pot
    ├── src/
    └── pyproject.toml
```

### Useful Links

- https://jupyterlab.readthedocs.io/en/latest/extension/internationalization.html
- https://github.com/jupyterlab/jupyterlab-translate
- https://github.com/jupyterlab/language-packs/#adding-a-new-extension