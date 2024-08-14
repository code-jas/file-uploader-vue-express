
# File Uploader - Vue.js & Express.js

This repository provides a full-stack application that demonstrates chunked file uploads using Vue.js on the frontend and Express.js on the backend. This is ideal for handling large file uploads efficiently by splitting them into manageable chunks. This project was developed as part of an **internship activity**, showcasing practical implementation of chunked file uploads.

## Features

-   **Chunked Uploads**: Upload large files by splitting them into smaller chunks.
-   **Vue.js Frontend**: A responsive and interactive frontend built with Vue.js.
-   **Express.js Backend**: A robust backend using Express.js to handle chunk processing and file assembly.
-   **Error Handling**: Comprehensive error handling during file uploads and chunk assembly.

## Technologies Used

-   **Frontend**:
    -   Vue.js 3
    -   Axios for HTTP requests
    -   Tailwind CSS (optional for styling)
-   **Backend**:
    -   Express.js
    -   Multer for handling file uploads
    -   Node.js & npm

## Getting Started

### Prerequisites
-   Node.js and npm installed on your machine
-   Vue CLI (optional for running the frontend)

### Installation

1.  **Clone the repository**:
    ```bash
    git clone https://github.com/code-jas/file-uploader-vue-express.git
    cd file-uploader-vue-express
    ``` 
    
2.  **Install backend dependencies**:
    ```bash
    cd backend
    npm install
    ``` 
    
3.  **Install frontend dependencies**:
    ```bash
    cd ../frontend
    npm install
    ``` 
    

### Running the Application

1.  **Start the backend server**:

    ```
    cd backend
    npm devStart
    ``` 
    
2.  **Start the frontend development server**:
    

    ```
    cd ../frontend
    npm run dev
    ``` 
    
4.  **Access the application**: Open your browser and navigate to `http://localhost:3000` to view the file uploader.
    

### Usage

1.  Select a file to upload.
2.  The file will be split into chunks and uploaded to the server.
3.  The server will reassemble the chunks into the original file.
4.  Track the upload progress with the progress bar.

### File Structure

-   **frontend/**: Contains the Vue.js application.
-   **backend/**: Contains the Express.js server and API routes.
