# GitHub Setup and Deployment Guide

This guide explains how to upload your V-Model Automobile Testing documentation to GitHub and deploy it to GitHub Pages.

## Prerequisites

- Git installed on your computer
- GitHub account created
- Repository created on GitHub (e.g., `automobile-testing-vmodel`)

## Step 1: Initialize Git Repository

Open a terminal in the `C:\Git\DaC` directory and run:

```bash
git init
git add .
git commit -m "Initial commit: V-Model automobile testing documentation"
```

## Step 2: Connect to GitHub Repository

Replace `YOUR_USERNAME` and `YOUR_REPO` with your GitHub username and repository name:

```bash
git remote add origin https://github.com/YOUR_USERNAME/YOUR_REPO.git
git branch -M main
git push -u origin main
```

## Step 3: Enable GitHub Pages

1. Go to your repository on GitHub
2. Click **Settings** tab
3. Click **Pages** in the left sidebar
4. Under **Build and deployment**:
   - Source: Select **GitHub Actions**
5. Save the settings

## Step 4: Automatic Documentation Build

The included GitHub Actions workflow (`.github/workflows/docs.yml`) will automatically:

1. Build the Sphinx documentation when you push to main branch
2. Deploy the HTML to GitHub Pages
3. Make it available at: `https://YOUR_USERNAME.github.io/YOUR_REPO/`

## Step 5: Verify Deployment

After pushing, check the **Actions** tab in your GitHub repository to see the workflow status. Once complete, your documentation will be live!

## Manual Build (Optional)

To build documentation locally before pushing:

```bash
cd docs
make html
```

View locally by opening: `docs\_build\html\index.html`

## Making Updates

1. Edit documentation files in `docs/source/`
2. Commit and push changes:
   ```bash
   git add .
   git commit -m "Update documentation"
   git push
   ```
3. GitHub Actions will automatically rebuild and deploy

## Project Structure

```
DaC/
├── .github/
│   └── workflows/
│       └── docs.yml          # GitHub Actions workflow
├── docs/
│   ├── source/               # Documentation source files
│   │   ├── index.rst         # Main documentation page
│   │   ├── conf.py           # Sphinx configuration
│   │   ├── v-model/          # V-Model documentation
│   │   ├── requirements/     # Requirements documentation
│   │   ├── design/           # Design documentation
│   │   ├── testing/          # Testing documentation
│   │   └── validation/       # Validation documentation
│   ├── Makefile              # Linux/Mac build
│   └── make.bat              # Windows build
├── requirements.txt          # Python dependencies
├── README.md                 # Project overview
└── .gitignore                # Git ignore rules
```

## Troubleshooting

### Build Fails

Check the Actions tab for error messages. Common issues:
- Missing dependencies in requirements.txt
- Syntax errors in .rst files
- Broken cross-references

### Pages Not Showing

1. Verify GitHub Pages is enabled in repository settings
2. Check that Actions workflow completed successfully
3. Wait a few minutes for DNS propagation
4. Clear browser cache

### Local Build Issues

Install dependencies:
```bash
pip install -r requirements.txt
```

## Customization

### Change Theme

Edit `docs/source/conf.py`:
```python
html_theme = 'alabaster'  # or 'sphinx_rtd_theme', 'sphinx_book_theme', etc.
```

### Add Content

1. Create new .rst files in appropriate directories
2. Add to toctree in parent index.rst
3. Build and test locally
4. Commit and push

### Modify Workflow

Edit `.github/workflows/docs.yml` to:
- Change Python version
- Add additional build steps
- Deploy to different branch

## Additional Resources

- [Sphinx Documentation](https://www.sphinx-doc.org/)
- [reStructuredText Primer](https://www.sphinx-doc.org/en/master/usage/restructuredtext/basics.html)
- [GitHub Pages Documentation](https://docs.github.com/en/pages)
- [GitHub Actions Documentation](https://docs.github.com/en/actions)

## Support

For issues or questions:
1. Check existing documentation
2. Review GitHub Actions logs
3. Consult Sphinx documentation
4. Open an issue in the repository
