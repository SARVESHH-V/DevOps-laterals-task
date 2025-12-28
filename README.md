# DevOps Laterals Task

Basically, I have implemented **LEVEL-1** of the project.  
Still a lot of optimization can be done, but I have not done it and kept it for the last.  
I have taken care of **Dockerization** and implementation of **Nginx reverse proxy**.

---

## Tech Stack

### Backend
- Rust  
- Actix-web  
- Diesel ORM  
- PostgreSQL  

### Frontend
- React  

### DevOps
- Docker  
- Docker Compose  
- Nginx (Reverse Proxy)  
- Git & GitHub  

---

## How to Run the Project

### Pre-requisites
1. Docker  
2. Docker Compose  
3. Git  

### Steps
1. Clone the repository  
2. Run the following command in bash:
   ```bash
   docker compose up -d
Open the browser and go to:

arduino
Copy code
http://localhost
Problems Faced and Solutions
1. Rust Compile Failure Inside Docker
Incompatible with the present Rust version, we were not able to compile properly (time crate – E0282).
Solved by updating the time crate to >=0.3.35.

2. Cargo Fetch and Build Failure
Build failed because cargo fetch and cargo build were run before /src existed in the image.
Solved by reordering the Dockerfile steps.

3. Diesel Command Not Found
Diesel migration failed on Windows and inside the container because Diesel CLI was not installed and backend runtime did not include Diesel.
Solved by modifying the Dockerfile accordingly.

4. Diesel Migrations Directory Not Found
Migrations directory was not found because containers are isolated and migrations/ existed only in the project root.
Solved by mounting /backend files into the container.

5. Relation Users Does Not Exist
Login/Register failed due to missing tables.
Solved by ensuring migrations were executed correctly.

6. Database Connection Errors
Used localhost inside the DB container, which is incorrect in Docker networking.
Solved by using the service name instead.

7. Service, Container, and Folder Name Confusion
Spent significant time confused between folder names, service names, and container names.
Solved by understanding Docker Compose service-based networking.

8. Port Confusion
Initially exposed ports 3000, 8080, and 80 directly.
Later restricted exposure to only port 80 (Nginx), keeping frontend and backend ports internal.
Nginx now routes all requests properly.

9. SPA Routing Issue
/ worked, but /login and /register failed.
Nginx was trying to find physical files for SPA routes.
Solved by adding SPA fallback configuration in frontend Nginx.

10. SPA Routing Changes Not Reflecting
No effect was seen after SPA routing fix because pre-built images were being used.
Solved by rebuilding and pulling the updated images.

11. Register Button Not Working
API routing did not match reverse proxy rules.
Solved by inspecting Network requests in browser DevTools and fixing routing rules.

12. Login / Register Returned 405
POST requests returned 405 because backend requests were routed to frontend.
Solved by routing backend APIs under /api/*.

13. Git Issues
Faced some basic Git issues but resolved them easily.

14. Compose File Usage Confusion
Initially used local build in compose.yml.
Later understood the CI/CD perspective and moved towards an image-based approach, which is more scalable and production-ready.