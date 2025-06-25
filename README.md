# Android web installer

This is an easy-to-use web installer for Android-based operating systems that runs entirely in the browser. It uses WebUSB, which is supported by Chromium and its derivatives, and my [fastboot.js](https://github.com/kdrag0n/fastboot.js) library for flashing. This allows it to work on computers and Android devices software with no additional software or command-line tools.

If you have a device officially supported by [ProtonAOSP](https://github.com/ProtonAOSP), you can try flashing it with the official instance of this web installer at [ProtonAOSP web installer](https://protonaosp.org/install/web/?utm_source=github&utm_campaign=android-webinstall).

![Screenshot of an install in progress on a desktop computer](https://user-images.githubusercontent.com/7930239/107459937-10c41e80-6b0c-11eb-8fbc-6882145f164f.png)

## Install process

The following screenshots show the entire installation process, from start to finish:

### Desktop

![Step: Prepare your device](https://user-images.githubusercontent.com/7930239/107459556-516f6800-6b0b-11eb-93b6-a3726c1d6256.png)
![Step: Choose an install type](https://user-images.githubusercontent.com/7930239/107459558-5207fe80-6b0b-11eb-80b7-5597e640bb0c.png)
![Step: Connect your device](https://user-images.githubusercontent.com/7930239/107459568-56341c00-6b0b-11eb-9f44-2760d873c8d7.png)
![Step: Unlock your bootloader](https://user-images.githubusercontent.com/7930239/107459571-57654900-6b0b-11eb-9a6e-1b83a9c8bb7b.png)
![Step: Download a build](https://user-images.githubusercontent.com/7930239/107459576-57fddf80-6b0b-11eb-82cb-731b35c6a10b.png)
![Step: Install ProtonAOSP](https://user-images.githubusercontent.com/7930239/107459937-10c41e80-6b0c-11eb-8fbc-6882145f164f.png)
![Step: Installation complete](https://user-images.githubusercontent.com/7930239/107459580-59c7a300-6b0b-11eb-831f-8d55e7c4c5ef.png)

## Adapting for other ROMs

By default, this installer is configured for the ProtonAOSP production environment, but it was built with reusability in mind. It can be adapted to other ROM projects with only configuration changes.

You can start with the following Yarn commands:

- `yarn install`
- `yarn serve`
- `yarn build`
- `yarn lint`

All configuration is in `src/config.js` and `.env`, and the release index at `public/releases/index.json` is fetched from the root of the domain hosting the installer. You may also want to change the favicon at `public/favicon.png`.

## Running with Docker (Recommended for Deployment)

This project can be easily run using Docker and Docker Compose for consistent environments and simplified deployment.

1.  **Ensure Docker and Docker Compose are installed.**
    *   [Install Docker](https://docs.docker.com/get-docker/)
    *   [Install Docker Compose](https://docs.docker.com/compose/install/)

2.  **Build and run the application:**
    ```bash
    docker-compose up --build
    ```
    This command will build the Docker image (if it doesn't exist or if changes were made) and start the service.

3.  **Access the application:**
    Open your web browser and navigate to `http://localhost:8080`.

To stop the application, press `Ctrl+C` in the terminal where `docker-compose up` is running, and then run:
```bash
docker-compose down
```

### Configuration when using Docker

When running with Docker, configuration is managed as follows:

*   **`public/releases/index.json`**: This file provides the list of available releases. It is volume-mounted from `./public/releases/index.json` on your host machine to `/usr/share/nginx/html/releases/index.json` in the container (read-only). Changes to the local file will be reflected in the running application.
*   **`public/favicon.png`**: The site favicon. It is volume-mounted from `./public/favicon.png` on your host machine to `/usr/share/nginx/html/favicon.png` in the container (read-only). Changes to the local file will be reflected (you might need to clear your browser cache).
*   **`src/config.js`**: This file contains core application JavaScript configuration. It is volume-mounted from `./src/config.js` on your host to `/app/src/config.js` in the container (read-only).
    *   **Important**: `src/config.js` is part of the application's build process. Any changes made to this file on your host **require the Docker image to be rebuilt** to take effect. Use the command: `docker compose build web-installer` (or `docker compose up --build`).
*   **Environment Variables (formerly `.env` settings)**: Application settings previously in `.env` (like `VUE_APP_OS_NAME` and `VUE_APP_OS_DESC`) are now configured directly in the `docker-compose.yml` file under the `environment` section for the `web-installer` service.
    ```yaml
    services:
      web-installer:
        # ... other settings
        environment:
          VUE_APP_OS_NAME: "Your OS Name"
          VUE_APP_OS_DESC: "Your OS Description"
          # Add other VUE_APP_* variables here
    ```
    Modify these values in `docker-compose.yml` and restart the container (`docker compose up -d --force-recreate`) for changes to take effect. If new variables are added that the application code expects from `process.env`, ensure they are prefixed with `VUE_APP_` for Vue CLI projects to pick them up during the build process if they are to be statically embedded. For runtime environment variables, the application must be coded to use `process.env.VARIABLE_NAME` directly.

## Contributing

Contributions are welcome! If you adapt this installer or make other improvements to it, please contribute the improvements back to the official repository instead of forking it and keeping the changes to yourself. There are many rough edges that need to be improved upon.
