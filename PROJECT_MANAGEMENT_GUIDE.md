# Project Management Guide

This document explains how to create, manage, and standardize projects in this home development environment.

## Overview

All projects in `/home/kevin/projects/` are managed through a centralized dashboard at **http://localhost:5000** with Cockpit system management at **https://localhost:9090**.

## Current Projects

| Project | Type | Port | Service | Status |
|---------|------|------|---------|--------|
| **Project Dashboard** | systemd | 5000 | project-dashboard.service | ✅ Running |
| **Finance Tracker** | systemd | 8080 | finance-tracker.service | ✅ Running |
| **Money Flow** | systemd | 5001 | money-flow.service | ✅ Running |
| **Honeypot** | systemd | 8000 | honeypot-api.service | ✅ Running |
| **Whisper Voice** | desktop | - | - | ℹ️ Desktop App |
| **Machine Learning** | data | - | - | ℹ️ Data Storage |

## Adding New Projects

### 1. Web Services (Recommended)

**Step 1: Create Project Structure**
```bash
mkdir /home/kevin/projects/my-new-project
cd /home/kevin/projects/my-new-project

# Set up Python environment
python3 -m venv venv
source venv/bin/activate
pip install flask  # or your dependencies
```

**Step 2: Create Service File**
```bash
sudo nano /etc/systemd/system/my-new-project.service
```

```ini
[Unit]
Description=My New Project Flask Application
After=network.target

[Service]
Type=simple
User=kevin
WorkingDirectory=/home/kevin/projects/my-new-project
ExecStart=/home/kevin/projects/my-new-project/venv/bin/python app.py
Restart=always
RestartSec=10
Environment=FLASK_ENV=production

[Install]
WantedBy=multi-user.target
```

**Step 3: Enable Service**
```bash
sudo systemctl daemon-reload
sudo systemctl enable my-new-project.service
sudo systemctl start my-new-project.service
```

**Step 4: Add to Dashboard**
Edit `/home/kevin/projects/project-dashboard/app.py` and add to the `PROJECTS` dictionary:

```python
"my-new-project": {
    "name": "My New Project",
    "description": "Description of your new project",
    "port": 5002,  # Choose an unused port
    "service": "my-new-project.service",
    "type": "systemd",
    "url": "http://localhost:5002",
    "path": "/home/kevin/projects/my-new-project"
}
```

**Step 5: Restart Dashboard**
```bash
sudo systemctl restart project-dashboard.service
```

### 2. Desktop Applications

For desktop apps (like Whisper Voice), add to dashboard as:

```python
"my-desktop-app": {
    "name": "My Desktop App",
    "description": "Desktop application",
    "port": None,
    "service": None,
    "type": "desktop",
    "url": None,
    "path": "/home/kevin/projects/my-desktop-app"
}
```

### 3. Data Projects

For data storage projects, add to dashboard as:

```python
"my-data-project": {
    "name": "My Data Project",
    "description": "Data processing project",
    "port": None,
    "service": None,
    "type": "data",
    "url": None,
    "path": "/home/kevin/projects/my-data-project"
}
```

### 4. Docker Projects

For Docker-based services, add to dashboard as:

```python
"my-docker-project": {
    "name": "My Docker Project",
    "description": "Docker-based service",
    "port": 5003,
    "service": None,
    "type": "docker",
    "url": "http://localhost:5003",
    "path": "/home/kevin/projects/my-docker-project",
    "docker_compose": "docker-compose.yml"
}
```

## Port Management

### Reserved Ports
- **5000**: Project Dashboard
- **5001**: Money Flow
- **8000**: Honeypot
- **8080**: Finance Tracker

### Available Ports
- **5002-5009**: Available for new web services
- **8010-8019**: Available for new services
- **8090-8099**: Available for new services

## Service Management Commands

### Check Status
```bash
# All services
sudo systemctl status project-dashboard.service finance-tracker.service money-flow.service honeypot-api.service

# Specific service
sudo systemctl status my-new-project.service
```

### Start/Stop/Restart
```bash
# Start
sudo systemctl start my-new-project.service

# Stop
sudo systemctl stop my-new-project.service

# Restart
sudo systemctl restart my-new-project.service
```

### View Logs
```bash
# Live logs
sudo journalctl -u my-new-project.service -f

# Recent logs
sudo journalctl -u my-new-project.service --no-pager -l
```

### Enable/Disable Auto-start
```bash
# Enable auto-start on boot
sudo systemctl enable my-new-project.service

# Disable auto-start
sudo systemctl disable my-new-project.service
```

## Dashboard Features

### Access Points
- **Dashboard**: http://localhost:5000
- **Cockpit**: https://localhost:9090

### Dashboard Capabilities
- ✅ **Service Status**: Real-time monitoring of all projects
- ✅ **Service Control**: Start/stop/restart with one click
- ✅ **Port Monitoring**: Check if services are listening
- ✅ **Service Logs**: View action history
- ✅ **Auto-refresh**: Updates every 30 seconds
- ✅ **Cockpit Integration**: Direct link to system management

### Project Types Supported
- **systemd**: Web services managed by systemctl
- **docker**: Docker Compose services
- **script**: Script-based services (legacy)
- **desktop**: Desktop applications
- **data**: Data storage projects

## Troubleshooting

### Service Won't Start
1. Check logs: `sudo journalctl -u service-name.service -l`
2. Verify paths in service file
3. Check virtual environment exists
4. Verify dependencies are installed

### Port Conflicts
1. Check what's using the port: `sudo lsof -i :PORT`
2. Change port in service configuration
3. Update dashboard configuration

### Permission Issues
1. Verify service file ownership: `sudo chown root:root /etc/systemd/system/service-name.service`
2. Check file permissions: `sudo chmod 644 /etc/systemd/system/service-name.service`

### Dashboard Issues
1. Restart dashboard: `sudo systemctl restart project-dashboard.service`
2. Check dashboard logs: `sudo journalctl -u project-dashboard.service -f`
3. Verify project configuration in `app.py`

## Best Practices

### Project Structure
```
/home/kevin/projects/
├── project-dashboard/          # Dashboard management
├── my-new-project/            # Your new project
│   ├── venv/                  # Virtual environment
│   ├── app.py                 # Main application
│   ├── requirements.txt       # Dependencies
│   └── README.md             # Project documentation
└── ...
```

### Naming Conventions
- **Service files**: `project-name.service`
- **Project folders**: `project-name` (lowercase, hyphens)
- **Dashboard IDs**: `project-name` (lowercase, hyphens)

### Security Notes
- Services run as user `kevin`
- systemctl commands require sudo
- Dashboard runs on localhost only
- Consider firewall rules for production use

## Quick Reference

### Add New Web Service
1. Create project folder
2. Set up virtual environment
3. Create systemd service file
4. Enable and start service
5. Add to dashboard configuration
6. Restart dashboard

### Remove Project
1. Stop and disable service: `sudo systemctl disable service-name.service`
2. Remove service file: `sudo rm /etc/systemd/system/service-name.service`
3. Remove from dashboard configuration
4. Restart dashboard
5. Delete project folder (optional)

### Update Project
1. Stop service: `sudo systemctl stop service-name.service`
2. Update code
3. Update dependencies if needed
4. Start service: `sudo systemctl start service-name.service`

This standardized approach ensures all projects are easily manageable, monitorable, and maintainable from a single dashboard interface. 