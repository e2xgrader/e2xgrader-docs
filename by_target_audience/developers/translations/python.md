Python files may be translated similarly to [TypeScript files](./typescript.md). The translator is obtained via an import
rather than dependency injection. The translation bunble can be obtained in the same way as in TypeScript, by calling the
load function, passing the desired translation domain.



```python
from jupyterlab_server.translation_utils import translator

#[...]

async def disable(self, plugins: str | list[str]) -> ActionResult:
        plugins = plugins if isinstance(plugins, list) else [plugins]
        locked = self._find_locked(plugins)
        trans = translator.load("jupyterlab")
        if locked:
            return ActionResult(
                status="error",
                message=trans.gettext("The following plugins cannot be disabled as they are locked: ")  # 'The following plugins cannot be disabled as they are locked: ' -> 'Die nachfolgenden Plugins können nicht deaktiviert werden, weil sie gesperrt sind: '
                + ", ".join(locked),
            )
```