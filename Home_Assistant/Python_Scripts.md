# Python Scripts
Created Thursday 19 September 2024

REF: <https://www.home-assistant.io/integrations/python_script/>

This integration allows you to write Python scripts that are exposed as actions in Home Assistant. Each Python file created in the <config>/python_scripts/ folder will be exposed as an action. The content is not cached so you can easily develop: edit file, save changes, perform action. The scripts are run in a sandboxed environment. The following variables are available in the sandbox:

