The InboxTester Application
Frontend (React)

User interface for sending test emails and viewing results
Form for entering sender email, subject, content, and test recipients
Real-time status updates as results come in
Comprehensive reporting dashboard

Backend (Node.js)

Email sending system using nodemailer
Reporting mechanism where recipients can indicate where emails landed
Multiple reporting options (link-based, form-based)
Integration with Google Postmaster Tools and Microsoft Defender
Results storage and aggregation

Key Features

Real-world testing: Sends actual emails to real recipients for true deliverability insights
Multiple reporting methods: Recipients can easily report placement via links or forms
Comprehensive analytics: Authentication checks, reputation monitoring, and placement tracking
Actionable recommendations: Provides specific suggestions to improve deliverability

Deployment Guide
I've also created a detailed Cloudflare deployment guide that explains:

How to set up the email processing backend on a traditional server
How to create a Cloudflare Worker as an API gateway
How to deploy the React frontend to Cloudflare Pages
How to configure DNS and security settings
How to set up monitoring and maintenance procedures

User Guide
The user guide explains how to use the application, including:

Creating and configuring tests
How recipients report where emails landed
Analyzing test results and recommendations
Working with test history and exporting data

This solution offers significant advantages over traditional deliverability testing:

It provides actual placement data rather than simulated results
It works with any email service provider
It gathers real user feedback about rendering issues
It helps identify specific factors affecting deliverability


# Email Deliverability Testing App Implementation Guide

## Project Overview

The Email Deliverability Testing App is a comprehensive tool designed to test how emails are delivered across multiple email service providers including Gmail, Google Workspace, Outlook, Hotmail, and Yahoo. It tracks whether emails land in the primary inbox, promotional tabs, spam folder, or other locations, and integrates with both Google Postmaster Tools and Microsoft Defender for Office 365.

## System Architecture

The application consists of two main components:

1. **Frontend**: A React-based web application with an intuitive UI for submitting test emails and viewing results
2. **Backend**: A Node.js/Express API server that handles:
   - Sending test emails to various service providers
   - Checking email placement via IMAP
   - Retrieving data from Google Postmaster and Microsoft Defender
   - Generating comprehensive reports

## Setup Instructions

### Prerequisites

- Node.js (v14+) and npm
- Access to test email accounts across different providers (Gmail, Outlook, etc.)
- SMTP server access for sending test emails
- [Optional] Google Postmaster Tools account with your domain verified
- [Optional] Microsoft 365 account with appropriate permissions

### Backend Setup

1. Clone the repository:
```bash
git clone https://github.com/swagner2/inboxtests
cd email-deliverability-app/backend
```

2. Install dependencies:
```bash
npm install
```

3. Configure environment variables by creating a `.env` file:
```
PORT=3001
SMTP_HOST=your-smtp-server.com
SMTP_PORT=587
SMTP_USER=your-username
SMTP_PASS=your-password
GOOGLE_CLIENT_ID=your-google-client-id
GOOGLE_CLIENT_SECRET=your-google-client-secret
GOOGLE_REFRESH_TOKEN=your-google-refresh-token
MICROSOFT_TENANT_ID=your-microsoft-tenant-id
MICROSOFT_CLIENT_ID=your-microsoft-client-id
MICROSOFT_CLIENT_SECRET=your-microsoft-client-secret
```

4. Configure test accounts by editing the `config/test-accounts.js` file:
```javascript
module.exports = {
  gmail: {
    email: 'your-test-gmail@gmail.com',
    imap: {
      user: 'your-test-gmail@gmail.com',
      password: 'your-app-password',
      host: 'imap.gmail.com',
      port: 993,
      tls: true
    }
  },
  // Configure additional email providers...
};
```

5. Start the backend server:
```bash
npm start
```

### Frontend Setup

1. Navigate to the frontend directory:
```bash
cd ../frontend
```

2. Install dependencies:
```bash
npm install
```

3. Configure the API endpoint by creating a `.env` file:
```
REACT_APP_API_URL=http://localhost:3001/api
```

4. Start the development server:
```bash
npm start
```

## Technical Implementation Details

### Email Testing Process

1. **Test Email Generation**:
   - The system creates a unique test email with tracking identifiers
   - Authentication headers (SPF, DKIM, DMARC) are properly set
   - Optional rendering tests (images, CSS) can be included

2. **Delivery Testing**:
   - The email is sent via SMTP to multiple test accounts
   - Each test account is monitored via IMAP to check where the email lands
   - Delivery time and placement are recorded

3. **Authentication Validation**:
   - SPF record validation
   - DKIM signature verification
   - DMARC policy check
   - Additional checks for BIMI, ARC, etc.

4. **Reputation Analysis**:
   - Integration with Google Postmaster Tools API
   - Integration with Microsoft Defender for Office 365
   - Analysis of sender reputation, complaint rates, and spam signals

### Database Schema

The application stores test results in a database with the following schema:

```
tests
  - id (UUID)
  - sender_email (string)
  - subject (string)
  - content (text)
  - created_at (timestamp)

test_results
  - id (UUID)
  - test_id (foreign key to tests.id)
  - provider (string, e.g., "gmail", "outlook")
  - placement (string, e.g., "inbox", "promotions", "spam")
  - delivery_time_ms (integer)
  - authentication_results (JSON)
  - rendering_issues (JSON array)
  - created_at (timestamp)
```

## Security Considerations

1. **Credentials Management**:
   - All email account credentials should be stored securely
   - Use environment variables or a secure key vault
   - Never commit credentials to version control

2. **API Security**:
   - Implement rate limiting to prevent abuse
   - Use API keys for authentication
   - Consider implementing JWT authentication for the frontend-backend communication

3. **Email Content Security**:
   - Sanitize all user inputs to prevent XSS and injection attacks
   - Avoid sending sensitive information in test emails
   - Include clear indicators that these are test emails

## Production Deployment

### Backend Deployment

1. Set up a production server (e.g., AWS EC2, Heroku, or Digital Ocean)
2. Configure environment variables for production
3. Set up a process manager (e.g., PM2) to keep the application running
4. Configure HTTPS with a valid SSL certificate
5. Set up monitoring and logging

### Frontend Deployment

1. Build the production bundle:
```bash
npm run build
```

2. Deploy the static files to a CDN or web server
3. Configure the production API endpoint
4. Set up HTTPS and appropriate caching headers

## Advanced Features

### Spam Trigger Analysis

The system can analyze emails for common spam triggers:
- Spammy words and phrases
- Excessive use of capital letters or special characters
- Image-to-text ratio analysis
- Link reputation checking

### Comparison Reports

Users can compare multiple test runs to track improvements over time:
- Delivery rate trends
- Placement changes
- Authentication improvements

### Automated Testing

Schedule regular tests to monitor ongoing deliverability:
- Daily or weekly test runs
- Alerts for deliverability issues
- Trend analysis over time

## API Documentation

### Backend API Endpoints

#### Test Email Deliverability

```
POST /api/test-email
```

Request body:
```json
{
  "senderEmail": "sender@yourdomain.com",
  "emailSubject": "Test Email Subject",
  "emailContent": "<html><body>Email content here</body></html>",
  "testAccounts": ["gmail", "outlook", "yahoo"]
}
```

Response:
```json
{
  "success": true,
  "results": [
    {
      "provider": "gmail",
      "status": "delivered",
      "landingFolder": "INBOX",
      "deliveryTime": 2354,
      "passed": true
    },
    ...
  ]
}
```

#### Get Google Postmaster Data

```
GET /api/postmaster/:domain
```

Response:
```json
{
  "domain": "yourdomain.com",
  "timeRange": "30d",
  "data": {
    "spamRate": 0.023,
    "authenticationRate": 0.987,
    "deliveryErrors": 0.005,
    "feedbackLoopRate": 0.001,
    "domainReputation": "HIGH"
  }
}
```

#### Get Microsoft Defender Data

```
GET /api/defender/:domain
```

Response:
```json
{
  "domain": "yourdomain.com",
  "timeRange": "30d",
  "data": {
    "ipReputation": "Good",
    "domainReputation": "High",
    "spamDetections": 12,
    "phishingDetections": 0,
    "malwareDetections": 0,
    "deliverySuccessRate": 0.998
  }
}
```

## Troubleshooting

### Common Issues

1. **Email Authentication Failures**:
   - Verify that SPF, DKIM, and DMARC records are properly configured
   - Check that the sending IP is not blocklisted

2. **IMAP Connection Issues**:
   - Verify account credentials and server settings
   - Check if less secure app access is enabled (for Gmail)
   - Confirm that IMAP is enabled for the account

3. **API Integration Problems**:
   - Verify API credentials and permissions
   - Check rate limits for the services being used
   - Confirm that the domain is properly verified

## Maintenance and Updates

### Regular Maintenance Tasks

1. Rotate API credentials periodically
2. Update dependencies to their latest secure versions
3. Monitor system performance and scale as needed
4. Keep test accounts active and in good standing

### Future Enhancements

1. Support for additional email providers
2. Enhanced AI-based content analysis
3. Integration with additional deliverability tools
4. Mobile app for on-the-go monitoring

## Support and Resources

- GitHub Repository: [https://github.com/yourusername/email-deliverability-app](https://github.com/yourusername/email-deliverability-app)
- Documentation: [https://yourdomain.com/docs](https://yourdomain.com/docs)
- Issue Tracker: [https://github.com/yourusername/email-deliverability-app/issues](https://github.com/yourusername/email-deliverability-app/issues)
- Contact: support@yourdomain.com
