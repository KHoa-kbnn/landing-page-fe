# Frontend Landing Page

This repository contains a static landing page built with `index.html` and served by Nginx via Docker.

## Run with Docker

1. Build the image:

```bash
docker build -t frontend .
```

2. Run the container:

```bash
docker run --rm -p 80:80 frontend
```

3. Open the app in your browser:

```text
http://localhost
```

## Notes

- The frontend expects a backend API at `http://localhost:3000/khachhang`.
- This repo currently contains only static HTML and no backend server.
