# Interactive Tutorial Guide for Students

Welcome to the **First Datasette App** interactive tutorial! This guide will help you get the most out of learning with an AI assistant.

## What Makes This Tutorial Interactive?

Unlike traditional tutorials where you simply follow steps, this tutorial is designed as a **conversation between you and an AI assistant** (Claude Code or GitHub Copilot). The AI will:

- ✅ Ask you questions to check your understanding
- ✅ Guide you to discover concepts on your own
- ✅ Help you troubleshoot when you get stuck
- ✅ Encourage you to make design decisions
- ✅ Celebrate your progress and learning

## Getting Started

### Option 1: Using Claude Code (Recommended for Codespaces)

1. **Open in Codespaces**: Click the green "Use this template" button and choose "Open in a codespace"
2. **Install Claude Extension**: In the Codespaces VS Code interface:
   - Click the Extensions icon in the left sidebar
   - Search for "Claude Code"
   - Install the extension
3. **Start the Tutorial**: Open the chat panel and say:
   ```
   I'm ready to start the First Datasette App tutorial!
   ```

### Option 2: Using GitHub Copilot Chat

1. **Open in Codespaces**: Click the green "Use this template" button and choose "Open in a codespace"
2. **Verify Copilot**: Make sure GitHub Copilot is active (you should see the Copilot icon)
3. **Open Chat**: Click the chat icon or press `Ctrl+Shift+I` (Windows/Linux) or `Cmd+Shift+I` (Mac)
4. **Start the Tutorial**: In the chat, say:
   ```
   I'm ready to start the First Datasette App tutorial! Please read Claude.md for instructions.
   ```

## How the AI Will Guide You

### Interactive Checkpoints

Throughout the tutorial, you'll see sections like this:

> **🤔 Interactive Checkpoint**
>
> Before you begin: Discuss with your AI assistant...

When you see these:
1. **Don't skip them!** They're designed to deepen your understanding
2. **Ask your AI assistant** the questions listed
3. **Share your thoughts** - there are no wrong answers at this stage
4. **Discuss** until you feel ready to move forward

### Types of Interactions

**🤔 Think First**: Before running a command, predict what will happen
- Example: "Before running `git status`, ask your AI what you expect to see"

**💭 Pause and Discuss**: Explore concepts before learning about them
- Example: "What do you think 'version control' means?"

**💬 Design Decisions**: Make choices and discuss the reasoning
- Example: "What should you name your database? Why?"

**🔍 Troubleshooting**: When errors occur, work through them together
- Example: "What does this error message tell you?"

**✅ Reflections**: Check your understanding at the end of each act
- Example: "Can you explain what we learned in your own words?"

## Tips for Success

### Do's ✅

- **Ask questions freely**: If you don't understand something, ask!
- **Explain your thinking**: The AI learns how to help you better when you share your thought process
- **Experiment**: Try things! The AI can help you recover from mistakes
- **Take your time**: Learning is more important than speed
- **Engage with reflections**: They help solidify your understanding
- **Be honest about confusion**: It's better to ask than to pretend you understand

### Don'ts ❌

- **Don't skip the interactive checkpoints**: They're where the deep learning happens
- **Don't just copy commands**: Understand what each command does
- **Don't rush**: This isn't a race
- **Don't be afraid of errors**: They're learning opportunities
- **Don't work in silence**: Keep the conversation going with your AI assistant

## Example Interactions

### Good Interaction Example

**You**: "I'm ready to start Act 1!"

**AI**: "Great! Before we begin Act 1: Hello Codespaces, let me ask you a few questions. What do you hope to learn from this tutorial? And have you used Git or version control before?"

**You**: "I want to learn how to publish data on the web. I've heard of Git but never used it."

**AI**: "Perfect! That's exactly what we'll do. Git is a version control system that helps you track changes to your code. Think of it like 'track changes' in Microsoft Word, but much more powerful. What do you think might be useful about tracking changes to code?"

**You**: "Maybe so you can go back if you make a mistake? Or see what you changed?"

**AI**: "Exactly right! Those are two of the main benefits. Now let's get started with the Codespaces environment..."

### Less Effective Interaction Example

**You**: "I'm ready to start."

**AI**: "Great! Before we begin Act 1, have you used Git before?"

**You**: "No. What's the first command?"

**AI**: "Let me explain what Git is first. It's important to understand..."

**You**: "Just tell me what to type."

*[This approach misses the learning opportunities!]*

## Working Through Each Act

Each of the 5 acts follows a similar pattern:

1. **Introduction**: Discuss what you'll learn
2. **Exploration**: Try things and make predictions
3. **Implementation**: Run commands and build your project
4. **Understanding**: Explain concepts in your own words
5. **Reflection**: Summarize what you learned

### Typical Act Flow

```
Start Act
    ↓
Discuss goals and concepts
    ↓
Make predictions about what will happen
    ↓
Run commands and observe results
    ↓
Discuss what happened
    ↓
Troubleshoot any issues (with AI help)
    ↓
Reflect on what you learned
    ↓
Complete Act → Move to next
```

## Getting Help

### When You're Stuck

If you're confused or stuck:

1. **Tell your AI assistant**: "I'm stuck on this part. Can you help me understand?"
2. **Ask for clarification**: "Can you explain what X means?"
3. **Request examples**: "Can you give me an example of when this would be useful?"
4. **Ask for hints**: "Can you give me a hint without telling me the answer?"

### When You Get Errors

Don't panic! Errors are normal and valuable:

1. **Read the error message** carefully
2. **Discuss with your AI**: "I got this error. What do you think it means?"
3. **Work through it together**: The AI will guide you to solve it
4. **Learn from it**: Understanding errors makes you a better developer

## Tutorial Overview

Here's what you'll learn in each act:

- **Act 1: Hello Codespaces** - Development environment, Git basics, version control
- **Act 2: Hello sqlite-utils** - CSV data, databases, command-line tools
- **Act 3: Hello Datasette** - Web servers, publishing data, plugins
- **Act 4: Customizing Datasette** - Data types, database transformations
- **Act 5: Exploring Data** - Data analysis, facets, filters, SQL queries

## Expected Time

- **Fast pace**: 1-2 hours (if you have some programming experience)
- **Moderate pace**: 2-3 hours (recommended - don't rush!)
- **Thoughtful pace**: 3-4 hours (taking time to really understand everything)

**Remember**: The goal isn't to finish quickly - it's to understand deeply!

## Assessment and Understanding

Throughout the tutorial, the AI will check your understanding through questions. These aren't tests - they're learning tools! The AI uses your answers to:

- Identify areas where you might need more explanation
- Adjust the pace to match your learning
- Ensure you're building on solid understanding
- Help you connect concepts together

## After the Tutorial

When you complete the tutorial:

1. **Review your work**: Look back at what you've built
2. **Final reflection**: Discuss with the AI what you learned
3. **Explore further**: Ask about next steps and advanced topics
4. **Apply your knowledge**: Think about projects where you could use these skills

## Troubleshooting the Interactive Experience

### "The AI isn't asking me questions"

- Make sure the AI has read the `Claude.md` file
- Remind the AI: "Please use the interactive teaching style from Claude.md"
- Reference the interactive checkpoints in docs/index.rst

### "I just want the answers"

- Remember: Explaining concepts helps you learn them better
- The Socratic method (learning through questions) is proven effective
- You'll retain more if you think through problems rather than just copying solutions

### "This is taking too long"

- It's okay to take breaks!
- Learning deeply takes time
- You can save your progress and come back later (it's all saved in Git!)

## Tips from Past Students

> "At first I wanted to just rush through, but when I slowed down and really discussed things with the AI, I understood so much more!"

> "Don't be embarrassed to ask 'dumb questions' - the AI never judges and the explanations really helped."

> "The reflection questions at the end of each act helped me realize what I'd actually learned."

> "Making mistakes and working through errors with the AI taught me more than getting everything right the first time."

## Ready to Begin?

When you're ready to start:

1. Open the chat with your AI assistant
2. Say: **"I'm ready to start the First Datasette App tutorial!"**
3. Follow along with the tutorial content in `docs/index.rst`
4. Engage with the interactive checkpoints
5. Ask questions and enjoy the learning process!

## Additional Resources

- **Tutorial Content**: See `docs/index.rst` for the full tutorial
- **AI Instructions**: See `Claude.md` for how the AI is instructed to teach
- **Datasette Documentation**: https://datasette.io/
- **sqlite-utils Documentation**: https://sqlite-utils.datasette.io/
- **Git Tutorial**: https://git-scm.com/docs/gittutorial

---

**Remember**: This tutorial is a conversation, not a lecture. The more you engage, question, and explore, the more you'll learn. Your AI assistant is here to guide you, not just to give you answers.

Happy learning! 🚀
