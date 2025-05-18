# hospital-management-system-blockchain - README

*This documentation is from the private repository hospital-management-system-blockchain.*

---


# Project Overview

This repository contains the implementation of a decentralized hospital management system utilizing blockchain technology. The system ensures secure storage of patient records, appointments, prescriptions, and invoices within individual user blocks. The application is built using **Flask** as the backend framework and **React** for the frontend, with additional integrations for email verification, video conferencing, real-time chat, and payment processing.

## Features & Integrations

### 1. **Email & OTP Authentication**

* The system uses **Mailtrap** for email-based OTP verification.
* You must generate your own API keys from Mailtrap (free) and add them to the `app.py` file.

### 2. **Video Conferencing (Jitsi Meet)**

* Jitsi Meet is integrated for secure video conferencing.
* The setup requires multi-server support and successful integration.
* Obtain your own Jitsi Meet API keys and configure them accordingly.

### 3. **Chat Module**

* Real-time chat messages are stored locally in memory.
* Chat history is maintained within the running Flask terminal.

### 4. **Payment Processing (Stripe)**

* Stripe payment processing is integrated.
* The routes are successfully triggered.
* After registering a business and linking a bank account, proper Stripe APIs will be incorporated into the existing functions in `app.py` (Stripe section).

### 5. **Blockchain-Based Data Storage**

* The system employs a **JSON-based blockchain** as a decentralized database.
* Each registered user has a dedicated block that securely stores:
  * Appointments
  * Bookings
  * Prescriptions
  * Invoices

## Installation & Setup

### **1. Clone the Repository**

```bash
  git clone https://github.com/usmannaveed-19/Hopital-Management-System-master
  cd Hopital-Management-System-master
```

### **2. Install Backend Dependencies (Flask)**

* Ensure you have **Python** installed.
* All required backend libraries are listed in `requirements.txt`.
* Install dependencies by running:

```bash
  pip install -r requirements.txt
```

### **3. Install Frontend Dependencies (React)**

* Navigate to the frontend directory:

```bash
  cd frontend
```

* Install required packages:

```bash
  npm install
```

### **4. Environment Configuration**

* Generate your API keys for  **Mailtrap, Jitsi Meet, and Stripe** .
* Add them to the appropriate sections in `app.py`.

### **5. Running the Application**

To start the project, run the following two terminals simultaneously:

#### **Start the Flask Backend**

```bash
  python app.py
```

#### **Start the React Frontend**

```bash
  cd frontend
  npm start
```

Once both servers are running, the project will be fully operational.

## Project Structure

```
Hopital-Management-System-master/
│── backend/                   # Flask application
│   ├── app.py                 # Main backend application
│   ├── blockchain.json        # JSON-based blockchain storage
│   ├── requirements.txt       # Python dependencies
│── frontend/                  # React application
│   ├── src/
│   ├── package.json           # Frontend dependencies
│   ├── public/
│── README.md                  # Project documentation
```

## Contribution & Support

* Ensure all integrations work correctly before submitting pull requests.
* For issues, please open a **GitHub issue** with detailed information.
* Contributions to improve functionality or optimize performance are welcome.

## License

This project is open-source and available under the  **MIT License** .

---

For further inquiries or contributions, feel free to connect with the repository maintainers.
