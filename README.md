# Stockroom frontend

React + Vite inventory interface. See ../SETUP.md for local API startup, database migration, authentication and deployment.

Accounts with `users.role = 'user'` can list, search, and view product details. Accounts with `users.role = 'admin'` also have create, edit, delete, and inventory analytics. Registration offers User and Admin and saves the selected role. Permissions are checked against the database on every API request.

```powershell
npm ci
Copy-Item .env.example .env
npm run dev
```

Start MySQL in WAMP, then run `npm run dev:api` in a separate terminal from this directory. Open http://localhost:5173.

Development uses `VITE_API_URL=/api`; Vite proxies API requests to `API_PROXY_TARGET` (default `http://127.0.0.1:8000`). To use WAMP Apache instead, set `API_PROXY_TARGET=http://localhost/lab6_crud/public` and restart Vite. For a separately hosted production API, set `VITE_API_URL` to its full URL ending in `/api` and set the backend `FRONTEND_URL` to the frontend origin. Never put database credentials into frontend environment variables.

Production builds use `.env.production`, which points to `https://product-crud-react.onrender.com/api`. In Render, remove any `VITE_API_URL=/api` override or change it to this full backend URL, then redeploy the frontend. The development proxy does not run in production.

Use the Test API routes link to open https://api-tester.marasigan.dev/. See ../API-TESTING.md for routes and request examples.

## Deploy to Render with Docker

Push this directory, including `Dockerfile`, to your Git repository. In Render, create a **Web Service**, connect the repository, and select the **Docker** runtime. If this frontend is in a repository subdirectory, set the service's Root Directory to that subdirectory. Leave Docker Command empty to use the image's default command.

Set `VITE_API_URL` in Render's environment settings to your deployed backend URL ending in `/api` (for example, `https://your-api.onrender.com/api`) before deploying. Render passes this value as a Docker build argument; Vite embeds it during the build, so redeploy after changing it. Set the backend's `FRONTEND_URL` to your Render frontend URL to allow API requests.

The container serves the built frontend using Nginx on Render's `PORT` (default `10000`), with support for client-side routes. It does not include the PHP backend or MySQL database. The `/api` default is only useful in local development; production requires the separately hosted API URL.

To build and run locally with Docker:

```powershell
docker build --build-arg VITE_API_URL=https://your-api.onrender.com/api -t stockroom-frontend .
docker run --rm -p 10000:10000 stockroom-frontend
```
