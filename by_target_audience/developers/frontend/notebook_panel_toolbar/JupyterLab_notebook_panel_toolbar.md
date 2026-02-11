JupyterLab's notebook panel toolbar is deeply intertwined with the many packages,
that implement JupyterLab's frontend (see [UML class diagram](NootebookToolbarClasses.puml) & [UML sequence diagram](NotebookToolbarSequence.puml)).

All the items visible on the notebook toolbar are defined via JSON schema files. The default set of toolbar items is defined
in the [notebook-extension](https://github.com/jupyterlab/jupyterlab/blob/3b5ba131a971344b0ce4e49a1bb74bd589fad1fa/packages/notebook-extension/schema/panel.json#L5C6-L5C14).

``` json
"jupyter.lab.toolbars": {
    "Notebook": [
      { "name": "save", "rank": 10 },
      {
        "name": "insert",
        "command": "notebook:insert-cell-below",
        "icon": "ui-components:add",
        "rank": 20
      },
      { "name": "cut", "command": "notebook:cut-cell", "rank": 21 },
      { "name": "copy", "command": "notebook:copy-cell", "rank": 22 },
      { "name": "paste", "command": "notebook:paste-cell-below", "rank": 23 },
      {
        "name": "run",
        "command": "notebook:run-cell-and-select-next",
        "rank": 30
      },
      {
        "name": "interrupt",
        "command": "notebook:interrupt-kernel",
        "rank": 31
      },
      { "name": "restart", "command": "notebook:restart-kernel", "rank": 32 },
      {
        "name": "restart-and-run",
        "command": "notebook:restart-run-all",
        "rank": 33
      },
      { "name": "cellType", "rank": 40 },
      { "name": "spacer", "type": "spacer", "rank": 100 },
      { "name": "kernelName", "rank": 1000 },
      { "name": "executionProgress", "rank": 1002 }
    ]
  }
```
(excerpt of JupyterLab's JSON schema of the notebook extension [`packages/notebook-extension/schema/panel.json`])

The implementation (factory) of each kind of toolbar item needs to be registered beforehand. This is done via the toolbarWidgetRegistry (`IToolbarWidgetRegistry`).
Most factories of the default toolbar items are registered in the [notebook-extension](https://github.com/jupyterlab/jupyterlab/blob/9287d9d4e237d0616ede24f9e66ad49ede1afa03/packages/notebook-extension/src/index.ts#L1276C1-L1276C10).
Most of these factories implement toolbar items with unique functionalities like changing the cell-type or displaying the current execution status.
The `name` attribute from the JSON schema is used to differentiate between the different kind of items and their corresponding factory.
All names, that do exactly match a factory, are handled by the [default factory](https://github.com/jupyterlab/jupyterlab/blob/9287d9d4e237d0616ede24f9e66ad49ede1afa03/packages/apputils/src/toolbar/registry.ts#L129) of the ToolbarWidgetRegistry (inside `@jupterlab/apputils`).
This factory toolbar items, that are meant be reuesed multiple times. They are configured with the attributes from the JSON schema.
By default, the ToolbarFactory implements a command-button item and a spacer item. They are diffetiated by the `type` attribute
and gernerally configured via the JSON schema.