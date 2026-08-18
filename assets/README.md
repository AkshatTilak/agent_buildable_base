# assets/ — User-Provided Test Inputs

> **Purpose**: This folder holds random reusable scripts, docs, or input files
> that the user may need to provide for tests — such as PDFs, videos, audios,
> images, sample data, or reference documents.

## Important

- **This folder is gitignored.** The contents are NOT committed to version
  control (see `.gitignore`).
- Only this `README.md` file is tracked.
- Place user-provided test inputs here so tests can reference them without
  bloating the repository.

## Usage

- Add test input files (pdf, video, audio, images, scripts, docs) here.
- Reference them from tests via a path relative to this folder.
- Do not commit large or user-specific files.

## Structure

```
assets/
├── README.md          # this file (tracked)
├── pdfs/              # user-provided PDFs for tests
├── media/             # videos, audios, images for tests
├── scripts/           # reusable helper scripts
└── docs/              # user-provided reference documents
```

> Subfolders are created as needed. Only this README is tracked.
