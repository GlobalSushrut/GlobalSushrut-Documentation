# hospital-management-system-blockchain - README-lightweight

*This documentation is from the private repository hospital-management-system-blockchain.*

---

# Ultra-Lightweight Hospital Management System

This guide explains how to run your Hospital Management System in an ultra-lightweight mode that preserves all telemedicine capabilities while minimizing resource usage on your laptop.

## What's Included

1. **Mock Blockchain Service** - Simulates blockchain operations without heavy resource usage
2. **Lightweight Rust DB Proxy** - Provides mock data access without running the full Rust database
3. **Minimal Backend** - Runs essential API endpoints with reduced worker count
4. **Frontend with Telemedicine Features** - Preserves all enhanced telemedicine capabilities

## How to Run

1. Make the scripts executable:

```bash
chmod +x ultra-lightweight-stack.sh stop-lightweight-stack.sh
```

2. Start all services:

```bash
./ultra-lightweight-stack.sh
```

3. Access your application:
   - Frontend: http://localhost:3000
   - Diagnostic Dashboard: http://localhost:3000/#/diagnostic-page
   - API: http://localhost:5001/api/v1

4. To stop all services:

```bash
./stop-lightweight-stack.sh
```

## Telemedicine Features

All enhanced telemedicine capabilities are preserved in this lightweight mode:

1. **Doctor Details Modal in Marketplace.jsx**
   - Basic doctor information (name, specialization, experience)
   - Advanced capabilities (care continuity, remote monitoring, diagnostic network, hybrid care)
   - Regulatory compliance information

2. **Enhanced DoctorProfile.jsx**
   - Advanced Telemedicine Capabilities section
   - Regulatory Compliance section
   - Visual enhancements with color-coded badges and icons

## Troubleshooting

If you encounter any issues:

1. Check log files in `./data/mock-db/` directory
2. Ensure ports 3000, 5001, 7878, and 8545 are available
3. Verify that the environment variables are set correctly

## Performance Tips

1. Close other resource-intensive applications while testing
2. Use a lightweight browser
3. Access one feature at a time to minimize memory usage
