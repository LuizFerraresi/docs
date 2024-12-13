
# VS Code Config

## Install VS Code Extensions

This command will install all the Extensions listed on README file.

```bash
code --install-extension \
  bierner.github-markdown-preview \
  davidanson.vscode-markdownlint \
  github.copilot \
  github.copilot-chat \
  github.vscode-github-actions \
  ms-azuretools.vscode-docker \
  ms-kubernetes-tools.vscode-kubernetes-tools \
  ms-python.debugpy \
  ms-python.python \
  ms-python.vscode-pylance \
  redhat.vscode-yaml \
  techer.open-in-browser \
  yahyabatulu.vscode-markdown-alert \
  yzhang.markdown-all-in-one
```

## VS Code Customizations

Got to `File -> Preferences -> Settings`

### Custom Limit Line

```json
"editor.rulers": [
  {
    "column": 80,
    "color": "#ff00FF"
  },
]
```
