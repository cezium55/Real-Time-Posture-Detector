# Real-Time Posture Analytics Platform

A full-stack telemetry and visualization platform designed to monitor posture health, log session-level metrics, and stream real-time analytics using a highly concurrent WebSocket architecture.

![Python](https://img.shields.io/badge/Python-3776AB?style=for-the-badge&logo=python&logoColor=white)
![FastAPI](https://img.shields.io/badge/FastAPI-005571?style=for-the-badge&logo=fastapi)
![Next.js](https://img.shields.io/badge/Next.js-000000?style=for-the-badge&logo=next.js&logoColor=white)
![MongoDB](https://img.shields.io/badge/MongoDB-4EA94B?style=for-the-badge&logo=mongodb&logoColor=white)

---

## System Architecture

This project is built with a decoupled architecture, separating the heavy data-processing backend from the client-facing UI to ensure maximum performance and zero blocking during real-time inference.

* **Frontend (Client UI):** Next.js with TypeScript and Tailwind CSS. Handles the user dashboard, real-time metric visualization, and camera stream interfacing.
* **Backend (Inference & API):** Python powered by FastAPI. Manages the computer vision posture detection logic (`posture_detector.py`) and serves the REST API.
* **Real-Time Data Layer:** WebSockets stream the live telemetry data (posture angles, warnings, frame states) from the Python backend to the Next.js frontend with sub-millisecond latency.
* **Database (Persistence):** MongoDB with the `Motor` asynchronous driver. Designed with a flexible NoSQL schema to rapidly ingest continuous session-level data points without locking the event loop.

---

## Core Features

* **Asynchronous Telemetry Streaming:** Utilizes FastAPI's native WebSocket support to stream dense computer vision data to the client without overloading traditional HTTP polling.
* **Non-Blocking I/O:** The entire backend is built on Python's `asyncio`. Database reads/writes use Motor to yield control back to the event loop, ensuring the API remains highly responsive even during heavy traffic.
* **Secure Session Management:** Features robust multi-tenant authentication using PyJWT and Argon2 password hashing via `passlib`, ensuring user metric isolation.
* **Dynamic Visualizations:** The Next.js frontend parses the WebSocket stream to render live health metrics, historical session logs, and actionable posture correction alerts.

---

## Local Development Setup

Follow these steps to spin up the decoupled environment locally.

### 1. Database Configuration
Ensure you have MongoDB running locally or a MongoDB Atlas URI. 
Create a `.env` file in the `posturedectbackend` directory:

```env
MONGO_URI=mongodb://localhost:27017
JWT_SECRET=your_super_secret_key
```

### 2. Start the Backend (FastAPI)
Navigate to the backend directory, activate your virtual environment, install the dependencies, and boot the ASGI server.

```bash
cd posturedectbackend
python -m venv .venv
source .venv/bin/activate  # On Windows use: .venv\Scripts\activate
pip install -r requirements.txt
uvicorn main:app --reload
```

### 3. Start the Frontend (Next.js)
Open a new terminal window, navigate to the frontend directory, install the node modules, and start the development server.

```bash
cd posturedectfrontend
npm install
npm run dev
```

### 4. Access the Application
* **Client UI:** `http://localhost:3000`
* **API Documentation (Swagger UI):** `http://localhost:8000/docs`
