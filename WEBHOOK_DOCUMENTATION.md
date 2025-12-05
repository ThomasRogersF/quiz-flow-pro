# Quiz Flow Pro - Enhanced Webhook Documentation

## Overview

The Quiz Flow Pro application sends quiz completion data to a configured webhook URL when users complete a quiz. This document describes the enhanced webhook payload format that includes time tracking, detailed answers, and submission information.

## Webhook Payload Structure

The webhook sends a POST request with a JSON payload containing the following fields:

```json
{
  "name": "User's name",
  "email": "user@example.com",
  "score": 15,
  "quizz-id": "Quizz-Flow-Pro",
  "submissionDate": "2025-12-05T22:15:51.834Z",
  "timeTakenSeconds": 345,
  "timeTakenFormatted": "05:45",
  "answers": [
    {
      "questionId": "q1",
      "questionText": "Choose the correct response to: ¿Cómo te llamas?",
      "userAnswer": "me_llamo_sebastian",
      "userAnswerText": "Me llamo Sebastian",
      "isCorrect": true,
      "timeSpentSeconds": 15
    }
  ],
  "totalQuestions": 25,
  "quizTitle": "Test Your Spanish Knowledge",
  "resultLevel": "B1 • Intermediate"
}
```

## Field Descriptions

### User Information
- **name** (string): The participant's name as submitted in the user information form
- **email** (string): The participant's email address as submitted in the user information form

### Quiz Results
- **score** (number): The number of correctly answered questions
- **totalQuestions** (number): The total number of questions in the quiz
- **resultLevel** (string, optional): The result level/title based on the participant's performance

### Quiz Metadata
- **quizz-id** (string): Static identifier for the quiz application ("Quizz-Flow-Pro")
- **quizTitle** (string): The title of the quiz as configured in the quiz settings

### Timing Information
- **submissionDate** (string): ISO 8601 timestamp when the quiz was submitted (YYYY-MM-DDTHH:mm:ss.sssZ)
- **timeTakenSeconds** (number): Total time taken to complete the quiz in seconds
- **timeTakenFormatted** (string): Total time taken in MM:SS format for easy display

### Answer Details
The **answers** array contains detailed information about each question:

- **questionId** (string): The unique identifier for the question (e.g., "q1", "q2")
- **questionText** (string): The full text of the question as presented to the user
- **userAnswer** (string | string[] | File | null): The raw answer value submitted by the user
- **userAnswerText** (string): The human-readable text representation of the user's answer
- **isCorrect** (boolean): Whether the answer matches the correct answer
- **timeSpentSeconds** (number): Time spent on this specific question in seconds

## Configuration

### Setting Up Webhook URL

1. Navigate to the Quiz Admin Panel
2. Go to the "General Settings" tab
3. Enter your webhook URL in the "Webhook URL" field
4. Save your changes

### Webhook Requirements

- **Method**: POST
- **Content-Type**: application/json
- **Response**: Should return HTTP 200-299 status code for successful processing

## Example Implementation

Here's an example of how to handle the webhook in Node.js:

```javascript
const express = require('express');
const app = express();

app.use(express.json());

app.post('/webhook', (req, res) => {
  const quizData = req.body;
  
  console.log(`Quiz completed by: ${quizData.name} (${quizData.email})`);
  console.log(`Score: ${quizData.score}/${quizData.totalQuestions}`);
  console.log(`Time taken: ${quizData.timeTakenFormatted}`);
  console.log(`Result level: ${quizData.resultLevel}`);
  
  // Process individual answers
  quizData.answers.forEach((answer, index) => {
    console.log(`Question ${index + 1}: ${answer.isCorrect ? '✓' : '✗'} (${answer.timeSpentSeconds}s)`);
  });
  
  // Store in database, send email, etc.
  
  res.status(200).send({ success: true });
});

app.listen(3000, () => {
  console.log('Webhook server running on port 3000');
});
```

## Error Handling

The application includes error handling for webhook submissions:

- If the webhook URL is not configured, quiz completion proceeds normally without data submission
- If the webhook returns an error status code, the user is shown a notification
- Network errors are logged and the user is informed of submission issues

## Security Considerations

- Ensure your webhook endpoint validates the incoming data
- Consider implementing authentication (API keys, signatures) for your webhook endpoint
- The webhook URL should use HTTPS to protect data in transit
- Validate and sanitize all received data before processing

## Version History

- **v1.0**: Basic webhook with name, email, score, and quiz ID
- **v2.0**: Enhanced webhook with timing information, detailed answers, and submission date (current version)