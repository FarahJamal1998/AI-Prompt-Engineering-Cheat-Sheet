# 🚀 Cursor AI Cheat Sheet - Your Ultimate Prompt Engineering Guide

> **Created by Farah Jamal** | *Your friendly neighborhood prompt engineer* 🎯

Welcome to the most comprehensive Cursor IDE prompt engineering cheat sheet! This project is your go-to resource for mastering AI-assisted development with Cursor.

## 🎯 Why This Exists

As developers, we're constantly looking for ways to code faster, smarter, and more efficiently. Cursor IDE has revolutionized how we write code, but knowing the right prompts can make the difference between a helpful AI assistant and a frustrating one.

This cheat sheet exists because:
- **Time is precious** - No more trial and error with prompts
- **Consistency matters** - Standardized prompts for better results
- **Learning curve** - New developers need a quick reference
- **Community sharing** - We all benefit from shared knowledge

## 📋 Quick Command Reference

| Command | What It Does | When To Use |
|---------|-------------|-------------|
| `Ctrl + K` | Generate code | Need new functions, components, or logic |
| `Ctrl + L` | Chat with AI | Ask questions, get explanations, debug |
| `Ctrl + I` | Inline edit | Fix specific lines or small code blocks |
| `Ctrl + Shift + L` | Multi-line edit | Edit multiple similar lines at once |
| `Ctrl + Shift + K` | Generate tests | Create unit tests, integration tests |
| `Ctrl + Shift + I` | Generate docs | Create JSDoc, README, comments |

## 🎨 Essential Prompts by Category

### 🚀 Productivity Prompts

```markdown
"Optimize this function for performance and readability"
"Add comprehensive error handling to this code"
"Convert this to use async/await pattern"
"Refactor this code to follow SOLID principles"
"Add TypeScript types to this JavaScript code"
```

### 🐛 Bug Fixing Prompts

```markdown
"Debug this code and explain what's wrong"
"Find potential memory leaks in this code"
"Identify and fix security vulnerabilities"
"Optimize this database query"
"Fix the race condition in this async code"
```

### 🧪 Testing Prompts

```markdown
"Generate unit tests for this function"
"Create integration tests for this API endpoint"
"Write test cases for edge cases"
"Generate mock data for testing"
"Create performance tests for this component"
```

### 📚 Documentation Prompts

```markdown
"Add JSDoc comments to this function"
"Create a README for this project"
"Generate API documentation"
"Write inline comments explaining the logic"
"Create user documentation for this feature"
```

## 💡 Pro Tips

### 1. **Be Specific**
❌ Bad: "Fix this code"
✅ Good: "Fix the memory leak in this React component by properly cleaning up event listeners"

### 2. **Provide Context**
```markdown
"Given this React component that handles user authentication, optimize it for performance and add proper error boundaries"
```

### 3. **Use Examples**
```markdown
"Refactor this function to use the same pattern as the getUserData function above"
```

### 4. **Iterate Incrementally**
- Start with a simple prompt
- Refine based on the output
- Ask for specific improvements

## 🔧 Real-World Examples

### Before & After Code Transformation

**Before (Messy Code):**
```javascript
function getData() {
  fetch('/api/data')
    .then(response => response.json())
    .then(data => {
      console.log(data);
      document.getElementById('result').innerHTML = data.name;
    })
    .catch(error => {
      console.error(error);
    });
}
```

**After (Clean Code with Cursor Prompt):**
```javascript
async function getData() {
  try {
    const response = await fetch('/api/data');
    if (!response.ok) {
      throw new Error(`HTTP error! status: ${response.status}`);
    }
    const data = await response.json();
    displayResult(data.name);
  } catch (error) {
    console.error('Failed to fetch data:', error);
    displayError('Unable to load data. Please try again.');
  }
}

function displayResult(name) {
  const resultElement = document.getElementById('result');
  if (resultElement) {
    resultElement.innerHTML = name;
  }
}

function displayError(message) {
  const resultElement = document.getElementById('result');
  if (resultElement) {
    resultElement.innerHTML = `<span class="error">${message}</span>`;
  }
}
```

*Prompt used: "Refactor this function to use async/await, add proper error handling, and separate concerns into smaller functions"*

## 📁 Project Structure

```
CursorAICheatSheet/
├── README.md                    # This file - your main reference
├── prompts/
│   ├── productivity-prompts.md  # Speed up your development
│   ├── react-prompts.md        # React-specific prompts
│   ├── testing-prompts.md      # Test generation prompts
│   └── bug-fixing-prompts.md   # Debug and fix issues
├── examples/
│   └── before-after-code/      # Real transformation examples
├── screenshots/
│   └── cursor-prompt-sample.png # Visual examples
├── certificate/
│   └── farah-udemy-cert.png    # Achievement unlocked! 🏆
└── LICENSE                      # MIT License
```

## 🎓 Advanced Techniques

### 1. **Chain Prompts**
```markdown
1. "Analyze this code for potential issues"
2. "Now fix the issues you identified"
3. "Add comprehensive tests for the fixed code"
```

### 2. **Context-Aware Prompts**
```markdown
"Given this is a React component in a TypeScript project using Redux, optimize it for performance"
```

### 3. **Role-Based Prompts**
```markdown
"Act as a senior React developer and review this code for best practices"
```

## 🚀 Getting Started

1. **Clone this repository**
   ```bash
   git clone https://github.com/yourusername/CursorAICheatSheet.git
   ```

2. **Open in Cursor IDE**
   - Install Cursor if you haven't already
   - Open the project folder

3. **Start experimenting**
   - Try the prompts in the cheat sheet
   - Customize them for your needs
   - Share your discoveries!

## 🤝 Contributing

Found a great prompt? Want to add more examples? Contributions are welcome!

1. Fork the repository
2. Add your prompt or example
3. Submit a pull request
4. Help the community grow! 🌱

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## 📚 Advanced Guides

For more specialized prompt engineering techniques, check out these focused guides:

### 🤖 [AI-Powered Development in Replit](advanced-guides/replit/README.md)
- Code refactoring and completion
- Auto-generate tests
- Project scaffolding
- Debug runtime issues
- Mock API creation

### 🚀 [GitHub Copilot Mastery](advanced-guides/github-copilot/README.md)
- Inline prompt patterns
- Best practices for code completion
- Comment-based triggers
- When NOT to trust Copilot

### 🧠 [ChatGPT Prompt Engineering](advanced-guides/chatgpt-prompt-engineering/README.md)
- Structured prompting techniques
- Avoiding vagueness
- System role mastery
- Step-by-step requests
- Handling common ChatGPT errors

### 🟦🔴 [AI Tricks for .NET & Angular](advanced-guides/ai-tricks-dotnet-angular/README.md)
- .NET code generation patterns
- EF Core optimization
- Angular component generation
- Reactive forms and state management
- Real prompts and sample completions

## 🏆 Certificate

*Farah Jamal* has successfully completed the **Advanced Prompt Engineering Course** and is certified to create comprehensive AI cheat sheets! 🎉

---

**Happy coding with Cursor! 🚀**

*Remember: The best prompt is the one that gets you the result you need. Keep experimenting and refining!* 