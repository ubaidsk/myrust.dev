# MyRust.dev

A practical FAQ guide for Python and C++ developers learning Rust.

## 🚀 Live Site

Visit the live site at: [https://ubaidsk.github.io/myrust.dev](https://ubaidsk.github.io/myrust.dev)

## 📚 About

This Jupyter Book answers the most common questions Python and C++ developers have when learning Rust. It focuses on:

- **Direct answers** without unnecessary introductions
- **Code comparisons** between Rust, Python, and C++
- **Practical examples** you can understand immediately
- **Simple explanations** suitable for an Indian audience

## 🤖 AI-Generated Content Notice

**Important:** This content was generated using AI and may contain errors, outdated information, or incomplete explanations. We encourage community contributions to improve accuracy and completeness.

## 🛠️ Development

### Prerequisites

- Python 3.8+
- pip

### Local Development

1. **Clone the repository:**
   ```bash
   git clone https://github.com/ubaidsk/myrust.dev.git
   cd myrust.dev
   ```

2. **Install dependencies:**
   ```bash
   pip install -r requirements.txt
   ```

3. **Build the book:**
   ```bash
   jupyter-book build .
   ```

4. **Preview locally:**
   ```bash
   # Open _build/html/index.html in your browser
   # Or serve it locally:
   python -m http.server 8000 --directory _build/html
   ```

### Making Changes

1. Edit the markdown files in the repository root
2. Build locally to test your changes
3. Commit and push to your fork
4. Create a pull request

## 🚀 Deployment Setup

This repository uses GitHub Actions for automatic deployment to GitHub Pages.

### First-time Setup

1. **Fork this repository** to your GitHub account

2. **Update the repository URL** in `_config.yml`:
   ```yaml
   repository:
     url: https://github.com/ubaidsk/myrust.dev
   ```

3. **Enable GitHub Pages:**
   - Go to your repository Settings
   - Navigate to "Pages" in the left sidebar
   - Under "Source", select "GitHub Actions"
   - Save the settings

4. **Push changes to main branch:**
   ```bash
   git add .
   git commit -m "Initial setup"
   git push origin main
   ```

5. **Wait for deployment:**
   - Check the "Actions" tab for build progress
   - Once complete, your site will be available at `https://ubaidsk.github.io/myrust.dev`

### Automatic Deployment

- **Every push to main** triggers a build and deployment
- **Pull requests** trigger builds but don't deploy (for testing)
- **Manual triggers** are available via the Actions tab

## 🤝 Contributing

We welcome contributions! Here's how you can help:

### Reporting Issues

- **Found an error?** [Open an issue](https://github.com/ubaidsk/myrust.dev/issues)
- **Confusing explanation?** Tell us what didn't make sense
- **Missing topic?** Suggest what should be added

### Contributing Content

1. Fork the repository
2. Create a new branch: `git checkout -b improve-memory-section`
3. Make your changes to the relevant `.md` files
4. Test locally: `jupyter-book build .`
5. Commit: `git commit -am "Improve memory management explanations"`
6. Push: `git push origin improve-memory-section`
7. Create a Pull Request

### Content Guidelines

- **Keep it simple:** Use short sentences and familiar analogies
- **Compare to known languages:** Always relate to Python or C++ when possible
- **No jargon first:** Explain concepts before introducing Rust-specific terms
- **Code examples:** Include working, minimal examples
- **Test your changes:** Build locally before submitting

## 📝 Content Structure

```
├── intro.md                    # Welcome page
├── getting-started.md          # Installation and first programs
├── memory-management.md        # Ownership, borrowing, lifetimes
├── data-structures.md          # Structs, enums, collections
├── error-handling.md           # Result types and error propagation
├── functions-behavior.md       # Functions, traits, generics
├── concurrency.md              # Threading and message passing
├── advanced-topics.md          # Macros, type system, comparisons
├── _config.yml                 # Jupyter Book configuration
├── _toc.yml                    # Table of contents
└── requirements.txt            # Python dependencies
```

## 📄 License

This content is available under the [MIT License](LICENSE). Feel free to use, modify, and distribute.

## 🙏 Acknowledgments

- Generated with the assistance of AI
- Inspired by the Rust community's excellent documentation
- Built with [Jupyter Book](https://jupyterbook.org/)

---

**Help us improve!** If you find errors or have suggestions, please [open an issue](https://github.com/ubaidsk/myrust.dev/issues) or contribute directly.
