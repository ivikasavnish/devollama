// package.json
{
  "name": "my-first-extension",
  "displayName": "My First Extension",
  "description": "A simple VSCode extension",
  "version": "0.0.1",
  "repository": {
    "type": "git",
    "url": "https://github.com/ivikasavnish/devollama.git"
  },
  "bugs": {
    "url": "https://github.com/yourusername/vscode-ollama/issues"
  },
  "homepage": "https://github.com/yourusername/vscode-ollama#readme",
  "engines": {
    "vscode": "^1.85.0"
  },
  "categories": [
    "Other"
  ],
  "activationEvents": [],
  "main": "./extension.js",
  "contributes": {
    "commands": [
      {
        "command": "my-first-extension.getCursorInfo",
        "title": "MyExt: Get Cursor Info"
      },
      {
        "command": "my-first-extension.getSurroundingText",
        "title": "MyExt: Get Surrounding Text"
      },
      {
        "command": "my-first-extension.getFileInfo",
        "title": "MyExt: Get File Info"
      },
      {
        "command": "my-first-extension.insertAtCursor",
        "title": "MyExt: Insert Text at Cursor"
      }
    ]
  },
  "scripts": {
    "lint": "eslint .",
    "pretest": "npm run lint",
    "test": "vscode test"
  },
  "devDependencies": {
    "@types/vscode": "^1.85.0",
    "@types/node": "18.x",
    "eslint": "^8.56.0",
    "typescript": "^5.3.3"
  }
}

// extension.js
const vscode = require('vscode');

/**
 * Get the current cursor position and selection
 * @returns {Object} Object containing position and selection info
 */
function getCursorAndSelection() {
    const editor = vscode.window.activeTextEditor;
    if (!editor) {
        return null;
    }

    const position = editor.selection.active;
    const selection = editor.selection;
    const wordRange = editor.document.getWordRangeAtPosition(position);
    const word = wordRange ? editor.document.getText(wordRange) : '';

    return {
        position: {
            line: position.line + 1, // Convert to 1-based line number
            character: position.character + 1
        },
        word,
        hasSelection: !selection.isEmpty,
        selectedText: editor.document.getText(selection)
    };
}

/**
 * Get text around the cursor with specified lines of context
 * @param {number} linesBefore Number of lines before cursor
 * @param {number} linesAfter Number of lines after cursor
 * @returns {string} Surrounding text
 */
function getSurroundingText(linesBefore = 3, linesAfter = 3) {
    const editor = vscode.window.activeTextEditor;
    if (!editor) {
        return null;
    }

    const position = editor.selection.active;
    const document = editor.document;
    
    const startLine = Math.max(0, position.line - linesBefore);
    const endLine = Math.min(document.lineCount - 1, position.line + linesAfter);
    
    const range = new vscode.Range(
        startLine, 0,
        endLine, document.lineAt(endLine).text.length
    );
    
    return editor.document.getText(range);
}

/**
 * Get information about the current file
 * @returns {Object} File information
 */
function getFileInfo() {
    const editor = vscode.window.activeTextEditor;
    if (!editor) {
        return null;
    }

    const document = editor.document;
    return {
        fileName: document.fileName,
        languageId: document.languageId,
        lineCount: document.lineCount,
        uri: document.uri.toString(),
        isUntitled: document.isUntitled,
        isDirty: document.isDirty
    };
}

function activate(context) {
    console.log('Extension "my-first-extension" is now active!');

    // Command to get cursor information
    let cursorInfoCommand = vscode.commands.registerCommand('my-first-extension.getCursorInfo', () => {
        const info = getCursorAndSelection();
        if (info) {
            const message = `Cursor Position: Line ${info.position.line}, Character ${info.position.character}\n` +
                          `Current Word: "${info.word}"\n` +
                          `Has Selection: ${info.hasSelection}\n` +
                          `Selected Text: "${info.selectedText}"`;
            vscode.window.showInformationMessage(message);
            console.log(info); // More detailed info in debug console
        } else {
            vscode.window.showInformationMessage('No active text editor');
        }
    });

    // Command to get surrounding text
    let surroundingTextCommand = vscode.commands.registerCommand('my-first-extension.getSurroundingText', async () => {
        const text = getSurroundingText();
        if (text) {
            // Create output channel if it doesn't exist
            const channel = vscode.window.createOutputChannel('MyExt Surrounding Text');
            channel.show();
            channel.appendLine('Surrounding Text (3 lines before and after cursor):');
            channel.appendLine('----------------------------------------');
            channel.appendLine(text);
        } else {
            vscode.window.showInformationMessage('No active text editor');
        }
    });

    // Command to get file information
    let fileInfoCommand = vscode.commands.registerCommand('my-first-extension.getFileInfo', () => {
        const info = getFileInfo();
        if (info) {
            const message = `File: ${info.fileName}\n` +
                          `Language: ${info.languageId}\n` +
                          `Lines: ${info.lineCount}`;
            vscode.window.showInformationMessage(message);
            console.log(info); // More detailed info in debug console
        } else {
            vscode.window.showInformationMessage('No active text editor');
        }
    });

    // Command to insert text at cursor
    let insertTextCommand = vscode.commands.registerCommand('my-first-extension.insertAtCursor', async () => {
        const editor = vscode.window.activeTextEditor;
        if (!editor) {
            vscode.window.showInformationMessage('No active text editor');
            return;
        }

        const text = await vscode.window.showInputBox({
            prompt: 'Enter text to insert at cursor',
            placeHolder: 'Text to insert'
        });

        if (text) {
            editor.edit(editBuilder => {
                editor.selections.forEach(selection => {
                    editBuilder.insert(selection.active, text);
                });
            });
        }
    });

    // Add all commands to subscriptions
    context.subscriptions.push(cursorInfoCommand);
    context.subscriptions.push(surroundingTextCommand);
    context.subscriptions.push(fileInfoCommand);
    context.subscriptions.push(insertTextCommand);
}

function deactivate() {}

module.exports = {
    activate,
    deactivate
}