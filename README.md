netstat -ano | findstr :8080
TCP    127.0.0.1:8080    0.0.0.0:0    LISTENING    1234
taskkill /PID 1234 /F
