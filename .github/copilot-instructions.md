# Copilot Instructions for Curso-Omicas-UNSAM

This document provides essential context for AI coding assistants working with this codebase.

## Project Overview
This is a course website built with MkDocs Material, focused on genomics and transcriptomics. The site is structured as an educational resource with theoretical and practical content.

## Key Architecture Components

### Content Structure
- `/docs/` - Main content directory
  - `/info/` - General course information
  - `/teoricas/` - Theoretical lessons
  - `/practicos/` - Practical exercises
  - `/instructivos/` - Instructions and guides
  - Images are stored in `imagenes/` subdirectories within relevant sections

### Configuration
- `mkdocs.yml` - Core configuration file
  - Defines site structure, navigation, and theme settings
  - Uses Material for MkDocs theme with Spanish language
  - Configured with search, video support, and tags functionality

## Development Workflow

### Local Development
```bash
# Setup environment
poetry install

# Run local development server
poetry shell
mkdocs serve  # Access at http://127.0.0.1:8000
```

### Content Creation Patterns
1. New content should be added as Markdown files in appropriate `/docs/` subdirectories
2. Images should be placed in `imagenes/` folders adjacent to related content
3. Navigation must be updated in `mkdocs.yml` under the `nav:` section

## Project-Specific Conventions

### Markdown Usage
- Files use Spanish language content
- Each practical/theoretical section follows standard structure:
  - `index.md` for main content
  - Supporting images in `imagenes/` subdirectory

### Theme Customization
- Custom color schemes defined in `mkdocs.yml`
- Supports light/dark mode toggle
- Uses Saira font for text and Roboto Mono for code

## Integration Points
- Netlify deployment (configured via `netlify.toml`)
- Poetry dependency management (`pyproject.toml`)
- Social media links configured in `mkdocs.yml` under `extra:`

For more details on customization, refer to [Material for MkDocs documentation](https://squidfunk.github.io/mkdocs-material/).