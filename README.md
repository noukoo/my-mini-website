# My Mini Website Pipeline

This project has been created for educational reasons. The main focus here is pushing the locally created index file to the internet, making it public for all users. 

In detail, this is a containerized web page with a fully automated CI/CD pipeline: every push to GitHub builds a Docker image, publishes it to Docker Hub and redeploys the live site.

https://my-mini-website-latest.onrender.com/ 

## Project Overview

This project is a simple web page served from a Docker container. The page itself is intentionally basic, because the goal is the automation around it. When I push a change to the `main` branch, GitHub Actions builds a new Docker image, pushes it to Docker Hub, and triggers Render to deploy it, with no manual steps.

## Technologies Used

| Tool | Why I chose it |
|---|---|
| **HTML** | A minimal website, so the focus stays on the pipeline |
| **Docker + Nginx** | Packages the site into a portable container that runs the same on any machine; Nginx is a lightweight, common web server |
| **Git + GitHub** | Version control and hosting for the code |
| **GitHub Actions** | CI/CD built into GitHub, free for public repos and configured with a single YAML file |
| **Docker Hub** | A public registry to store and share the built image |
| **Render** | A free hosting platform that can run Docker images directly |

## Architecture

```
Developer (push code)
        │
        ▼
     GitHub  ──────────►  GitHub Actions
                                │
                     1. build Docker image
                     2. push image
                                ▼
                           Docker Hub
                                │
                     3. deploy hook triggered
                                ▼
                             Render  ──►  Live website
```

## Implementation Steps

1. Set up an Ubuntu virtual machine and installed Docker and Git.
2. Created `index.html` and a `Dockerfile` based on `nginx:alpine`.
3. Built and ran the image locally to test it (`docker build`, `docker run -p 8080:80`).
4. Created a GitHub repository and pushed the code.
5. Created a Docker Hub access token and stored it, with my username, as GitHub Actions secrets.
6. Wrote `.github/workflows/deploy.yml` with two jobs: `build-and-push` and `deploy`.
7. Created a web service on Render from the Docker Hub image and connected its deploy hook as a secret.
8. Tested the whole flow by changing the page text, pushing, and watching the live site update.

## Challenges

- **Docker permission denied:** `docker run` failed with a permission error. I learned that my user had to be in the `docker` group, added it, and rebooted the VM.
- **Git "repository not found":** my push failed because of a stray `.` at the end of the remote URL. I fixed it with `git remote set-url` and learned to read error messages character by character.
- **Merge conflict in `deploy.yml`:** the file was changed both on GitHub and on my VM. I learned how Git merges work and resolved the conflict by restoring the correct version of the file.
- **Docker Hub login failing in the pipeline:** the pipeline returned "incorrect username or password". I tested the credentials locally with `docker login`, created a new access token and re-entered the secrets, which fixed it.
- **YAML indentation:** my `deploy` job was indented too far, so it was treated as part of the wrong job. I learned that spacing defines structure in YAML.

## Future Improvements

- Add automated tests to the pipeline before the image is built
- Tag images with the commit ID instead of only `latest`, so I can roll back
- Add monitoring and uptime alerts for the live site
- Provision the hosting infrastructure with Terraform
- Move from Render to Kubernetes to learn orchestration



