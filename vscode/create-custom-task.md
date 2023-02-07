# Define custom task

Write the code below in the `tasks.json` file in the `.vscode` folder

```json
{
	"version": "2.0.0",
	"tasks": [
		{
			"label": "npm: start",
			"type": "npm",
			"script": "start",
			"detail": "ng build --watch=true"
		}
	]
}
```

# Bind custom task with shortcut

Create the section below in the `keybindings.json` file. Please note that the property `args` must match the label in the custom task.

```json
{
	"key": "shift+alt+e",
	"command": "workbench.action.tasks.runTask",
	"args": "npm: start"
}
```

---

_Source: https://go.microsoft.com/fwlink/?LinkId=733558_
