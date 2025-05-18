# hospital-management-system-blockchain - VideoConferencingTestGuide

*This documentation is from the private repository hospital-management-system-blockchain.*

---

# MediChain Video Conferencing Test Guide

This document provides a comprehensive guide for testing the video conferencing features in the MediChain system, focusing on the JitsiMeet integration.

## Overview

MediChain's telemedicine system uses JitsiMeet as the primary video conferencing provider. The implementation includes:

1. Frontend React components for video/audio calls
2. Backend Flask API endpoints for managing video sessions 
3. Integration with blockchain for verifying doctor telemedicine capabilities
4. Session recording and blockchain verification for telemedicine consultations

## Frontend Components Testing

### VideoCall Component

The `VideoCall.jsx` component integrates with JitsiMeet for video conferencing. Testing focuses on:

- Initialization of JitsiMeet API
- Configuration for regular and telemedicine-specific calls
- Event handling
- Integration with backend API endpoints

### Test Suite Structure

The frontend tests are located in:
- `test/video-conferencing-test.js` - Comprehensive tests for all aspects of video conferencing
- `test/jitsi-integration-test.js` - Targeted tests for JitsiMeet integration

### Running the Frontend Tests

```bash
# Install test dependencies
npm install --save-dev sinon jsdom

# Run all video conferencing tests
npm test test/video-conferencing-test.js

# Run only JitsiMeet integration tests
npm test test/jitsi-integration-test.js
```

## Backend API Testing

The Flask backend provides API endpoints for video conferencing under the `/api/v1/video` route.

### Key API Endpoints

- `POST /api/v1/video/create-room` - Create a new video conferencing room
- `POST /api/v1/video/join-room/<room_name>` - Join an existing room
- `POST /api/v1/video/leave-room/<room_name>` - Leave a room
- `GET /api/v1/video/active-sessions` - Get all active sessions for a user
- `POST /api/v1/video/session/<session_id>/recording` - Toggle recording for a session

### Test Suite Structure

Backend tests are located in:
- `flask-backend/tests/test_video_api.py` - Tests for Flask API endpoints

### Running the Backend Tests

```bash
# From the flask-backend directory
python3 -m unittest tests/test_video_api.py
```

## Blockchain Integration Testing

For telemedicine sessions, the system integrates with the blockchain to:

1. Verify doctor telemedicine capabilities before starting a session
2. Record completed telemedicine sessions on the blockchain
3. Verify session authenticity for compliance and auditing

### Testing Blockchain Integration

This is tested in both frontend and backend test suites:

- Frontend: The `Telemedicine integration with Blockchain` section in `video-conferencing-test.js`
- Backend: The `test_telemedicine_integration` function in `test_video_api.py`

## Complete End-to-End Testing

For a comprehensive test of the entire video conferencing system:

1. Start the Flask backend server
2. Start the Express backend server
3. Deploy the smart contracts to a local blockchain
4. Run the frontend application
5. Log in as a doctor and patient
6. Initiate a telemedicine consultation
7. Verify the video call connects successfully
8. Complete the session and verify blockchain recording

## Debugging Video Call Issues

If issues occur during video conferencing tests:

1. **Frontend Issues:**
   - Check browser console for errors
   - Verify JitsiMeet API is loaded correctly
   - Ensure the room name is valid and unique

2. **Backend Issues:**
   - Check Flask logs for API errors
   - Verify session creation and management
   - Check authentication is working properly

3. **Blockchain Issues:**
   - Verify smart contracts are deployed correctly
   - Check for blockchain connection issues
   - Verify doctor telemedicine capability verification

## Additional Testing Scenarios

1. **Cross-browser Testing:**
   Test video conferencing on Chrome, Firefox, Safari, and Edge

2. **Mobile Testing:**
   Verify functionality on iOS and Android devices

3. **Network Conditions:**
   Test under various network conditions (bandwidth throttling)

4. **Scalability Testing:**
   Test with multiple participants in the same call

5. **Security Testing:**
   Verify encryption and authentication mechanisms

## Automated CI/CD Testing

For continuous integration testing of video conferencing:

```javascript
// Example GitHub Actions workflow
const workflow = {
  name: 'Video Conferencing Tests',
  on: ['push', 'pull_request'],
  jobs: {
    test: {
      runs-on: 'ubuntu-latest',
      steps: [
        { uses: 'actions/checkout@v2' },
        { uses: 'actions/setup-node@v2', with: { 'node-version': '16' } },
        { run: 'npm install' },
        { run: 'npm test test/video-conferencing-test.js' }
      ]
    }
  }
};
```

## Video Conferencing Feature Matrix

| Feature | JitsiMeet Support | Implemented | Tested |
|---------|-------------------|-------------|--------|
| Video Call | ✅ | ✅ | ✅ |
| Audio-only Call | ✅ | ✅ | ✅ |
| Screen Sharing | ✅ | ✅ | ✅ |
| Recording | ✅ | ✅ | ✅ |
| Chat During Call | ✅ | ✅ | ✅ |
| Multiple Participants | ✅ | ✅ | ✅ |
| Telemedicine Integration | N/A | ✅ | ✅ |
| Blockchain Verification | N/A | ✅ | ✅ |
| Mobile Support | ✅ | ✅ | ✅ |

## Future Enhancements

1. **Alternative Provider Support:**
   - Add support for alternative video conferencing providers like Twilio
   - Create an abstraction layer to easily switch providers

2. **Enhanced Recording Features:**
   - Implement automatic transcription of sessions
   - Add AI analysis of telemedicine consultations

3. **Advanced Telemedicine Features:**
   - Integration with medical devices for remote monitoring
   - Virtual waiting rooms for patients
