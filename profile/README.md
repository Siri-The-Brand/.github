# Siri The Brand
We are Siri The Brand

# **SSA-CORE Wiki**

## **Overview**
SSA-CORE is a FastAPI-based backend application designed to manage activities, solvers, and intelligences. It leverages modern cloud infrastructure, hosted on AWS EC2, and integrates with AWS DocumentDB for NoSQL data storage to handle deeply nested  and complex data structures. The application is deployed using a CI/CD pipeline configured in GitHub Actions.

---

## **Infrastructure**

### **Hosting and Deployment**
- **Hosting Platform**: The application runs on an **AWS EC2 instance**, managed within a Virtual Private Cloud (VPC).
- **Web Server**: The application uses **Uvicorn** as the ASGI server to serve the FastAPI application.
- **Deployment**: 
  - Managed through a **CI/CD pipeline** set up in **GitHub Actions**.
  - The pipeline pulls the latest changes from the repository and deploys them to the EC2 instance.
  - Secrets (database credentials and private keys) are stored securely in **GitHub Secrets**.

---

### **Database**
- **Type**: AWS DocumentDB (NoSQL) is used for data persistence due to its ability to handle **deeply nested JSON-like documents**.
- **Structure**:
  - The main collection is named **`activities`**.
  - This collection stores all activity-related data.
- **Connectivity**: The DocumentDB instance is linked to the EC2 instance through the VPC and configured subnets to ensure secure communication.

---

### **Networking**
- **VPC**: The EC2 and DocumentDB instances are part of a self-managed VPC to ensure secure and isolated communication.
- **Subnets**: Networking between resources is managed through configured subnets for scalability and security.

---

## **CI/CD Workflow**

### **Pipeline Triggers**
The CI/CD pipeline in GitHub Actions is triggered by:
1. **Pull Requests**: Automatically triggered when a pull request is made to the `main` branch.
2. **Manual Dispatch**: Allows developers to trigger the pipeline manually through the **"workflow_dispatch"** event.
3. **Scheduled Runs**: Scheduled to run automatically at **2:00 AM (UTC)** from Monday to Friday.

### **Pipeline Steps**
The pipeline is defined in the `deploy.yml` file with the following key steps:
1. **Checkout Code**:
   - Pulls the latest code from the GitHub repository.
2. **Setup Python**:
   - Configures Python version `3.10` for the environment.
3. **Install Dependencies**:
   - Installs project dependencies using `pip`.
4. **Run Tests**:
   - Executes all unit tests using `pytest` to ensure code quality before deployment.
5. **Deploy to EC2**:
   - Connects to the EC2 instance using **appleboy/ssh-action**.
   - Clones the repository, activates a virtual environment, installs dependencies, and runs the application.

### **Deployment Command**
The application is started on the EC2 instance using **Uvicorn**:
```bash
nohup uvicorn src.main:app --reload --port ${{ secrets.APP_PORT }} &
```

This command runs the application in the background, making it accessible at the port defined in GitHub Secrets.

---

## **Application Workflows**

### **Key Features**
The application supports endpoints for managing **activities**, **solvers**, and **intelligences**. Below are the endpoints:

1. **Activities**:
   - Base URL: `/activities`
   - Operations: Retrieve all activities or fetch by ID.

2. **Solvers**:
   - Base URL: `/solvers`
   - Operations: Manage solver-related data.

3. **Intelligences**:
   - Base URL: `/intelligences`
   - Operations: Retrieve or manage intelligence-related information.

### **Routing**
The following routes are included in the application:
```python
- router.include_router(intelligence_routes.router, prefix="/intelligences", tags=["Intelligences"])
- router.include_router(solver_routes.router, prefix="/solvers", tags=["Solvers"])
- router.include_router(activity_routes.router, prefix="/activities", tags=["Activities"])
```

---

## **Local Development Setup**
Refer to the `README.md` file in the repository for detailed instructions on setting up the application locally. Key steps include:
1. **Clone the Repository**:
   `git clone git@github.com:Siri-The-Brand/ssa-core.git`
   `cd ssa-core`
   

2. **Set Up a Virtual Environment**: 
   `python -m venv .venv`
   `source .venv/bin/activate`

3. **Install Dependencies**:
   `pip install -r requirements.txt`

4. **Environment Variables**:
   - Ensure that a `.env` file is created locally with the correct configurations.
   - If `.env` is unavailable, use `.env.example` as a reference to create it:
    `cp .env.example .env`

5. **Run the Application**:
   `uvicorn src.main:app --reload`


---

## **Troubleshooting**
### Common Errors
1. **ModuleNotFoundError: No module named 'server'**
   - Ensure the application is executed with the correct `PYTHONPATH`. For example:
     ```bash
     PYTHONPATH=src uvicorn src.main:app --reload
     ```

2. **Environment Variables Not Loaded**:
   - Confirm that the `.env` file exists in the project root.
   - Ensure the `.env` file is not ignored by `.gitignore`.

3. **Address Already in Use**:
   - If the application reports `Address already in use`, terminate the existing process running on the port:
     ```bash
     lsof -i :<PORT>
     kill <PID>
     ```

---

## **Conclusion**
This wiki provides an overview of SSA-CORE's infrastructure and workflows. For more information, refer to the project's documentation or contact the team lead. Ensure to keep the wiki updated with any changes to the application's architecture, deployment, or workflows that will be added.
