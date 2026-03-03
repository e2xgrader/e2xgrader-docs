In the TypeScript-frontend of your JupyterLab extension, you may use JupyterLab's translator to mark strings for translation.
An instance of the translator can be obtained via dependency-injection on plugin activation. Using the translator, a
translation bundle must be obtained via the load-function. When using the load-function a translation-domain must be specified.
This domain-name has to match the domain, that will be specified in the jupyter-translate command as well as subsequently
the names of the translation-files. Commonly the name of the python-extension is used as the translation domain.
The translation-bundle, taht has been loaded, must be stored in a variable, that is named `trans` ([or similar](https://jupyterlab.readthedocs.io/en/latest/extension/internationalization.html#rules)).
Otherwhise, your marked strings won't be recognized.

```typescript
//[...]

import { ITranslator } from '@jupyterlab/translation';

/**
 * Initialization data for the @e2xgrader/custom-notebook-toolbar:submit-command extension.
 */
export const submitCommandExt: JupyterFrontEndPlugin<void> = {
  id: '@e2xgrader/custom-notebook-toolbar:submit-command',
  description: 'adds a submit command.',
  requires: [
      INotebookTracker,
      ICommandPalette,
      ITranslator
  ],
  autoStart: true,
  activate: (app: JupyterFrontEnd, notebookTracker: INotebookTracker, commandPalette: ICommandPalette, translator: ITranslator) => {
    const trans = translator.load('e2xgrader_custom_notebook_toolbar'); //load the translation bundle matching the specified domain
    console.log(trans.__('registering submit command')); //this string is marked for translation
    
    app.commands.addCommand(SUBMIT_COMMAND_ID, new SubmitCommand(notebookTracker, trans)); //the translation bundle may be passed further on like shown here
    commandPalette.addItem({
      command: SUBMIT_COMMAND_ID,
      category: 'e2xgrader'
    });
  }
};
```

There are multiple ways of marking strings for translation the most common way is the simple `__()` command. This matches
a single string in the source language with one string in the translation language.

```typescript
trans.__('String to be translated'); // 'String to be translated' -> 'Zeichenkette, die übersetzt werden soll'
```

The `__()` command is also capable of performing string replacements. Indexed placeholders (`%<n>`) are replaced with additional 
arguments of the `__()` functions like shown here:

```typescript
trans.__('%1 is argument of a translated string', adjective); // '%1 is argument of a translated string' -> '%1 ist ein Argument einer übersetzten Zeichenkette'
```

There are even more translation functions like `_n()`. This example is capable of handling singular and plural variants
as  shown here:

```typescript
trans._n('Singular string for %1', 'Plural string for %1', n); // 'Singular string for %1' -> 'Singular Zeichenkette für %1'; 'Plural string for %1' -> 'Plural Zeichenkette für %1'

trans._n('one message', '%1 messages', n); // 'one message' -> 'eine Nachricht'; '%1 messages' -> '%1 Narichten'
```

