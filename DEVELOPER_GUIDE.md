# Developer Guide - HealthNest v2.0 API Reference

## Quick Code Examples for Developers

### 1. Appointment System

#### Book an Appointment:
```javascript
// User books appointment
const appointmentData = {
    doctor: "Dr. Rajesh Gupta",
    date: "2025-02-15",
    time: "10:00 AM",
    reason: "General checkup"
};

// Handler function
function handleAppointmentBooking(event) {
    event.preventDefault();
    appointments.push(appointmentData);
    displayAppointments();
    showToast('Appointment booked!', 'success');
}
```

#### Display Appointments:
```javascript
function displayAppointments() {
    const container = document.getElementById('appointmentCardsContainer');
    appointments.forEach(apt => {
        const card = document.createElement('div');
        card.className = 'appointment-card';
        card.innerHTML = `
            <h4>${apt.doctor}</h4>
            <div class="appointment-details">
                <p><strong>Date:</strong> ${new Date(apt.date).toLocaleDateString()}</p>
                <p><strong>Time:</strong> ${apt.time}</p>
            </div>
        `;
        container.appendChild(card);
    });
}
```

#### Generate Time Slots:
```javascript
function generateTimeSlots(selectedDate) {
    const times = ['09:00 AM', '10:00 AM', '11:00 AM', '12:00 PM', '02:00 PM'];
    const slotsContainer = document.getElementById('timeSlots');
    
    times.forEach(time => {
        const slot = document.createElement('div');
        slot.className = 'time-slot';
        slot.textContent = time;
        slot.onclick = function() {
            // Toggle selection
            document.querySelectorAll('.time-slot').forEach(s => s.classList.remove('selected'));
            this.classList.add('selected');
        };
        slotsContainer.appendChild(slot);
    });
}
```

---

### 2. Queue Management System

#### Initialize Queue:
```javascript
const queueSystem = {
    tokens: ['A001', 'A002', 'A003'],
    currentToken: 'A001',
    userToken: null,
    crowdLevel: 45,
    
    init: function() {
        this.updateQueueDisplay();
        this.startCrowdMonitoring();
        setInterval(() => this.simulateQueueProgress(), 5000);
    }
};

// Initialize
queueSystem.init();
```

#### Generate Token:
```javascript
function generateToken() {
    const nextNum = Math.floor(Math.random() * 1000).toString().padStart(3, '0');
    const token = 'A' + nextNum;
    queueSystem.userToken = token;
    return token;
}

// Usage:
const myToken = generateToken(); // Returns: "A047"
```

#### Update Queue Display:
```javascript
function updateQueueDisplay() {
    const tokenEl = document.getElementById('queueToken');
    const posEl = document.getElementById('queuePosition');
    const waitEl = document.getElementById('waitTime');
    
    if (queueSystem.userToken) {
        tokenEl.textContent = queueSystem.userToken;
        const position = 5; // Get from server
        posEl.textContent = position;
        waitEl.textContent = (position * 10) + ' minutes';
    }
}
```

---

### 3. Doctor Availability Tracking

#### Initialize Doctors:
```javascript
const doctors = [
    { 
        id: 'dr-gupta', 
        name: 'Dr. Rajesh Gupta', 
        specialty: 'General Physician', 
        status: 'available', 
        rating: 4.8 
    },
    { 
        id: 'dr-sharma', 
        name: 'Dr. Priya Sharma', 
        specialty: 'Cardiologist', 
        status: 'busy', 
        rating: 4.9 
    }
];
```

#### Render Doctor Cards:
```javascript
function initializeDoctorAvailability() {
    const grid = document.getElementById('doctorsGrid');
    
    doctors.forEach(doc => {
        const card = document.createElement('div');
        card.className = 'doctor-card';
        const statusClass = doc.status === 'available' ? 'available' : 'busy';
        
        card.innerHTML = `
            <div class="doctor-avatar">👨‍⚕️</div>
            <div class="doctor-name">${doc.name}</div>
            <div class="doctor-specialty">${doc.specialty}</div>
            <div class="availability-status ${statusClass}">
                ${doc.status.toUpperCase()}
            </div>
            <div>⭐ ${doc.rating}</div>
            <button class="cta-button" onclick="openModal('appointmentModal')">
                Book Now
            </button>
        `;
        
        grid.appendChild(card);
    });
}
```

#### Update Doctor Status:
```javascript
function updateDoctorStatus(doctorId, newStatus) {
    const doctor = doctors.find(d => d.id === doctorId);
    if (doctor) {
        doctor.status = newStatus; // 'available', 'busy', 'offline'
        initializeDoctorAvailability(); // Re-render
    }
}
```

---

### 4. Emergency Alert System

#### Handle Emergency:
```javascript
function handleEmergencyAlert(event) {
    event.preventDefault();
    
    const formData = {
        type: event.target.querySelector('select').value,
        description: event.target.querySelector('textarea').value,
        timestamp: new Date(),
        severity: 'critical'
    };
    
    // Send to hospital
    console.log('Emergency Alert:', formData);
    
    showToast('🚨 EMERGENCY ALERT SENT!', 'success');
    closeModal('emergencyAlertModal');
    
    // Notify hospital staff
    notificationManager.sendNotification(
        'EMERGENCY ALERT',
        formData.description,
        'error'
    );
}
```

#### Severity Levels:
```javascript
const emergencySeverities = {
    critical: {
        label: 'Critical - Immediate Care',
        color: '#e74c3c',
        priority: 1
    },
    serious: {
        label: 'Serious - Urgent Care',
        color: '#f39c12',
        priority: 2
    },
    moderate: {
        label: 'Moderate - Quick Attention',
        color: '#3498db',
        priority: 3
    }
};
```

---

### 5. Admin Dashboard

#### Switch Admin Tab:
```javascript
function switchAdminTab(tab) {
    const content = document.getElementById('adminContent');
    
    switch(tab) {
        case 'users':
            content.innerHTML = `
                <h3>Registered Users</h3>
                <table class="admin-table">
                    <tr>
                        <th>User ID</th>
                        <th>Name</th>
                        <th>Email</th>
                        <th>Status</th>
                    </tr>
                    <!-- Data rows -->
                </table>
            `;
            break;
            
        case 'doctors':
            content.innerHTML = `
                <h3>Doctor Management</h3>
                <table class="admin-table">
                    <tr>
                        <th>Doctor ID</th>
                        <th>Name</th>
                        <th>Specialty</th>
                        <th>Status</th>
                    </tr>
                    <!-- Data rows -->
                </table>
            `;
            break;
            
        // ... more cases
    }
}
```

---

### 6. Real-Time Notifications

#### Notification Manager:
```javascript
class NotificationManager {
    constructor() {
        this.notifications = [];
    }
    
    sendNotification(title, message, type = 'info') {
        // Browser notification
        if ('Notification' in window && Notification.permission === 'granted') {
            new Notification(title, {
                body: message,
                icon: '💚'
            });
        }
        
        // In-app toast
        showToast(`${title}: ${message}`, type);
    }
    
    requestPermission() {
        if ('Notification' in window && Notification.permission === 'default') {
            Notification.requestPermission();
        }
    }
}

// Usage:
const notificationManager = new NotificationManager();
notificationManager.sendNotification('Appointment Confirmed', 'Your appointment is scheduled', 'success');
```

---

### 7. Hospital Crowd Monitor

#### Crowd Monitoring:
```javascript
function startCrowdMonitoring() {
    setInterval(() => {
        // Simulate crowd level changes
        queueSystem.crowdLevel = Math.max(20, Math.min(95, queueSystem.crowdLevel + (Math.random() - 0.5) * 10));
        
        // Update UI
        const crowdFill = document.getElementById('crowdLevel');
        const crowdPercent = document.getElementById('crowdPercentage');
        
        crowdFill.style.width = queueSystem.crowdLevel + '%';
        crowdPercent.textContent = Math.round(queueSystem.crowdLevel);
        
        // Color coding
        if (queueSystem.crowdLevel < 40) {
            crowdFill.style.background = '#2ecc71'; // Green
        } else if (queueSystem.crowdLevel < 70) {
            crowdFill.style.background = '#f39c12'; // Yellow
        } else {
            crowdFill.style.background = '#e74c3c'; // Red
        }
    }, 3000);
}
```

#### Get Crowd Statistics:
```javascript
function getCrowdStats() {
    return {
        occupancy: queueSystem.crowdLevel,
        totalBeds: 200,
        occupiedBeds: Math.round(200 * queueSystem.crowdLevel / 100),
        availableBeds: Math.round(200 * (100 - queueSystem.crowdLevel) / 100),
        capacity: 'HIGH' // or LOW/MEDIUM
    };
}
```

---

### 8. Patient Registration

#### Validate Registration:
```javascript
function validateRegistration(formData) {
    const errors = [];
    
    if (!formData.name) errors.push('Name is required');
    if (!formData.email) errors.push('Email is required');
    if (!formData.dob) errors.push('Date of birth is required');
    if (!formData.phone) errors.push('Phone number is required');
    
    // Email validation
    const emailRegex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
    if (!emailRegex.test(formData.email)) {
        errors.push('Invalid email format');
    }
    
    return {
        isValid: errors.length === 0,
        errors: errors
    };
}

// Usage:
const validation = validateRegistration(formData);
if (!validation.isValid) {
    validation.errors.forEach(err => showToast(err, 'error'));
}
```

---

### 9. AI Assistant Enhancement

#### Get AI Response:
```javascript
function getAIResponse(userMessage) {
    const message = userMessage.toLowerCase();
    
    // Help topics
    const helpTopics = {
        'appointments': 'How to book appointments',
        'emergency': 'Emergency services info',
        'queue': 'Queue management help',
        'doctors': 'View doctor information',
        'prescriptions': 'Access prescriptions',
        'results': 'Check test results'
    };
    
    // Match user query to topics
    for (const [topic, help] of Object.entries(helpTopics)) {
        if (message.includes(topic)) {
            return `Information about ${help}. Click here to learn more.`;
        }
    }
    
    return "I'm not sure about that. Try asking about appointments, emergency, or doctors.";
}
```

#### Voice Command Handler:
```javascript
const voiceBtn = document.getElementById('ai-voice-btn');
const SpeechRecognition = window.SpeechRecognition || window.webkitSpeechRecognition;

if (SpeechRecognition) {
    const recognition = new SpeechRecognition();
    recognition.continuous = false;
    recognition.lang = 'en-US';
    
    voiceBtn.addEventListener('click', () => {
        recognition.start();
        voiceBtn.classList.add('recording');
    });
    
    recognition.onresult = (event) => {
        const transcript = event.results[0][0].transcript;
        const response = getAIResponse(transcript);
        showToast(response, 'info');
    };
}
```

---

### 10. Toast Notification

#### Show Toast:
```javascript
function showToast(message, type = 'info') {
    const container = document.getElementById('toast-container');
    const toast = document.createElement('div');
    toast.className = `toast ${type}`;
    
    let iconClass = 'fas fa-info-circle';
    if (type === 'success') iconClass = 'fas fa-check-circle';
    if (type === 'error') iconClass = 'fas fa-times-circle';
    
    toast.innerHTML = `<i class="${iconClass}"></i> ${message}`;
    container.appendChild(toast);
    
    // Show animation
    setTimeout(() => toast.classList.add('show'), 100);
    
    // Auto-hide after 3 seconds
    setTimeout(() => {
        toast.classList.remove('show');
        setTimeout(() => container.removeChild(toast), 500);
    }, 3000);
}

// Usage:
showToast('Appointment booked successfully!', 'success');
showToast('Error: Please fill all fields', 'error');
showToast('Waiting for response...', 'info');
```

---

### 11. Modal Management

#### Open/Close Modals:
```javascript
function openModal(modalId) {
    const modal = document.getElementById(modalId);
    if(modal) modal.classList.add('visible');
    
    // Focus first input
    modal?.querySelector('button, input, select, textarea')?.focus();
}

function closeModal(modalId) {
    const modal = document.getElementById(modalId);
    if(modal) modal.classList.remove('visible');
}

// Usage:
openModal('appointmentModal');
closeModal('appointmentModal');
```

---

## Database Schema Examples

### Appointments Table:
```sql
CREATE TABLE appointments (
    id INT PRIMARY KEY,
    patient_id INT,
    doctor_id INT,
    appointment_date DATE,
    appointment_time TIME,
    reason TEXT,
    status VARCHAR(20),
    created_at TIMESTAMP,
    FOREIGN KEY (patient_id) REFERENCES users(id),
    FOREIGN KEY (doctor_id) REFERENCES doctors(id)
);
```

### Queue Table:
```sql
CREATE TABLE queue (
    id INT PRIMARY KEY,
    token VARCHAR(10) UNIQUE,
    patient_id INT,
    appointment_id INT,
    position INT,
    status VARCHAR(20),
    created_at TIMESTAMP,
    called_at TIMESTAMP,
    FOREIGN KEY (patient_id) REFERENCES users(id)
);
```

### Hospital Statistics:
```sql
CREATE TABLE hospital_stats (
    id INT PRIMARY KEY,
    timestamp TIMESTAMP,
    total_patients INT,
    in_queue INT,
    being_served INT,
    completed INT,
    avg_wait_time INT,
    occupancy_percent DECIMAL(5,2)
);
```

---

## Integration Points for Backend

### API Endpoints Needed:
```
POST   /api/appointments           - Book appointment
GET    /api/appointments/:id       - Get appointment details
DELETE /api/appointments/:id       - Cancel appointment
GET    /api/doctors                - List all doctors
GET    /api/doctors/:id/availability - Get doctor schedule
POST   /api/queue/token            - Generate queue token
GET    /api/queue/:token           - Get queue status
POST   /api/emergency              - Report emergency
GET    /api/hospital/stats         - Get hospital statistics
POST   /api/notifications          - Send notification
```

---

## Best Practices

### State Management:
```javascript
// Use objects for related data
const appState = {
    user: { id, name, email },
    appointments: [],
    queue: { token, position },
    notifications: []
};
```

### Error Handling:
```javascript
try {
    const result = await bookAppointment(data);
    showToast('Appointment booked!', 'success');
} catch (error) {
    showToast(`Error: ${error.message}`, 'error');
    console.error('Booking error:', error);
}
```

### Performance:
```javascript
// Debounce updates
function debounce(func, delay) {
    let timeout;
    return function(...args) {
        clearTimeout(timeout);
        timeout = setTimeout(() => func(...args), delay);
    };
}

// Use on crowd monitoring
const updateCrowd = debounce(() => updateQueueDisplay(), 1000);
```

---

**Happy Coding! 🚀**
