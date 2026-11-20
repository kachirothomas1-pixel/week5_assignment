# Technical Writing Lab — Week 5 Assignment

This document contains the user manual procedure and API reference entry for the technical writing lab.

---

# A) User Manual Procedure

## Setting Up a GitHub Repository and Making the First Commit

### 1. Introduction

GitHub is an online platform for storing, managing, and sharing code. A repository is a project workspace that stores files and their revision history. This guide explains how a first-semester computing student can create a repository, save a first commit, and upload project files to GitHub.

### 2. Prerequisites

Before starting, make sure you have:

- A computer with an internet connection.
- A web browser, such as Google Chrome.
- A GitHub account and access to its login details.
- Git installed on your computer.
- A project folder containing at least one file.
- Basic knowledge of opening folders and using a terminal.

### 3. Procedure

Each numbered step below contains one action and its expected result.

1. **Open a web browser.**  
   Expected result: The browser window opens.

2. **Navigate to https://github.com.**  
   Expected result: The GitHub homepage appears.

3. **Sign in to your GitHub account.**  
   Expected result: Your GitHub dashboard appears.

4. **Select the plus (+) menu in the top-right corner.**  
   Expected result: A menu of repository actions appears.

5. **Select “New repository.”**  
   Expected result: The repository creation form appears.

6. **Enter a name in the Repository name field.**  
   Expected result: The chosen project name appears in the field.

7. **Choose either Public or Private visibility.**  
   Expected result: The selected visibility option is marked.

8. **Select “Create repository.”**  
   Expected result: GitHub displays the new, empty repository page.

9. **Open the project folder on your computer.**  
   Expected result: The folder and its project files are visible.

10. **Open a terminal in the project folder.**  
    Expected result: The terminal is ready to accept commands from that folder.

11. **Run `git init`.**  
    Expected result: Git initializes a local repository in the project folder.

12. **Run `git add .`.**  
    Expected result: The project files are staged for the next commit.

13. **Run `git commit -m "Initial commit"`.**  
    Expected result: Git creates a commit containing the staged files. If Git asks for your identity, configure your name and email with `git config --global user.name "Your Name"` and `git config --global user.email "you@example.com"`, then repeat the commit.

14. **Run `git branch -M main`.**  
    Expected result: The local branch is named `main`.

15. **Run `git remote add origin YOUR_REPOSITORY_URL`, replacing the placeholder with the HTTPS URL shown on your GitHub repository page.**  
    Expected result: The local repository has a remote named `origin` pointing to GitHub.

16. **Run `git push -u origin main`.**  
    Expected result: Git uploads the committed files to the GitHub repository. Complete the browser or credential prompt if GitHub requests authentication.

### 4. Screenshot Description

**Screenshot: Repository after the first successful push**

Include a screenshot of the GitHub repository's **Code** tab after the upload. It should show the repository name, the uploaded project file or files, the `main` branch selector, and the latest commit message (“Initial commit”). This demonstrates that the files are visible in the remote repository.

### 5. Troubleshooting

**Common beginner problem: Git reports that the current directory is not a Git repository.**

This usually happens when the terminal is open in the wrong folder or Git has not been initialized.

**Solution:**

1. Check that the terminal is open in the project folder.
2. Run `git init` in that folder.
3. Run `git status` to confirm that Git recognizes the repository.
4. Continue with the staging and commit steps.

### 6. Conclusion

You have created a GitHub repository, initialized Git locally, made a first commit, and uploaded the project files. The repository can now be used to track future changes and share the project.

---

# B) API Reference Entry

## Create a Task

### 1. Description

Creates a new task in an existing project in a project management application. The request must be authenticated and must include a title, assignee ID, due date, and priority. A description may also be supplied.

### 2. Request

| Property | Value |
|---|---|
| HTTP method | `POST` |
| Endpoint | `/v1/projects/{projectId}/tasks` |
| Base URL (example) | `https://api.example.com` |
| Content type | `application/json` |

**Full example URL:**

`https://api.example.com/v1/projects/PRJ-1001/tasks`

### 3. Path Parameters

| Name | Type | Required | Description |
|---|---|---|---|
| `projectId` | String | Yes | Unique identifier of the project in which the task will be created. |

### 4. Query Parameters

This endpoint does not use query parameters.

### 5. Request Body Parameters

Send a JSON object containing the following properties:

| Name | Type | Required | Description |
|---|---|---|---|
| `title` | String | Yes | Short name of the task. Must contain at least one non-whitespace character. |
| `description` | String | No | Additional details about the task. If omitted, the description is stored as an empty value or null according to the service's storage convention. |
| `assigneeId` | String | Yes | Unique ID of an existing user who will be assigned the task. |
| `dueDate` | String (date) | Yes | Task deadline in ISO 8601 calendar-date format: `YYYY-MM-DD`. |
| `priority` | String (enum) | Yes | Task priority. Allowed values are `low`, `medium`, and `high`. |

### 6. Request Headers

| Header | Required | Example | Description |
|---|---|---|---|
| `Authorization` | Yes | `Bearer <access_token>` | Supplies the access token used to authenticate the user. |
| `Content-Type` | Yes | `application/json` | Indicates that the request body is JSON. |
| `Accept` | Recommended | `application/json` | Indicates that the client expects a JSON response. |

The access token must be valid, and the authenticated user must have permission to create tasks in the specified project.

### 7. Example Request

**HTTP request:**

```http
POST /v1/projects/PRJ-1001/tasks HTTP/1.1
Host: api.example.com
Authorization: Bearer <access_token>
Content-Type: application/json
Accept: application/json
```

**JSON request body:**

```json
{
  "title": "Complete website homepage",
  "description": "Build the homepage layout and add the main navigation.",
  "assigneeId": "USR-204",
  "dueDate": "2026-12-15",
  "priority": "high"
}
```

### 8. Successful Response

**Status:** `201 Created`

The server returns the created task, including its generated ID and creation timestamp.

```json
{
  "success": true,
  "message": "Task created successfully.",
  "data": {
    "id": "TSK-5082",
    "projectId": "PRJ-1001",
    "title": "Complete website homepage",
    "description": "Build the homepage layout and add the main navigation.",
    "assigneeId": "USR-204",
    "dueDate": "2026-12-15",
    "priority": "high",
    "status": "pending",
    "createdAt": "2026-11-20T08:30:00Z"
  }
}
```

### 9. HTTP Response Codes

The following table defines the complete set of responses for this proposed API design.

| Status code | Name | When it occurs |
|---|---|---|
| `201` | Created | The task was created successfully. |
| `400` | Bad Request | The request is malformed or the JSON body cannot be parsed. |
| `401` | Unauthorized | The access token is missing, invalid, or expired. |
| `403` | Forbidden | The user is authenticated but does not have permission to create tasks in the project. |
| `404` | Not Found | The project or specified assignee cannot be found. |
| `409` | Conflict | The task cannot be created because the project is in a state that prevents new tasks, such as being archived. |
| `415` | Unsupported Media Type | The request uses a content type other than the supported JSON format. |
| `422` | Unprocessable Content | The JSON is valid, but one or more field values fail validation, such as an unsupported priority or incorrectly formatted due date. |
| `429` | Too Many Requests | The client has exceeded the API request limit. |
| `500` | Internal Server Error | An unexpected error occurred while processing the request. |
| `503` | Service Unavailable | The service is temporarily unavailable, for example during maintenance. |

### 10. Validation and Behaviour Notes

- The `title`, `assigneeId`, `dueDate`, and `priority` fields are required.
- The `description` field is optional.
- The title must not be blank.
- The assignee ID must identify an existing user.
- The due date must be a valid date in `YYYY-MM-DD` format.
- The priority must be exactly `low`, `medium`, or `high`.
- The project must exist and allow new tasks.
- The authenticated user must have permission to create tasks in the project.
- The server generates the task ID, initial status, and creation timestamp; clients should not submit these fields.

### 11. Conclusion

The Create a Task endpoint enables an authenticated user to add a task to an existing project. It accepts a JSON request, validates the supplied values and permissions, and returns the created task with a `201 Created` status. Clear validation rules and response codes help API consumers understand and handle success and error conditions.

---

## Assignment Completion Checklist

- [x] Part A: Prerequisites, step-by-step procedure, expected results, screenshot description, and troubleshooting.
- [x] Part B: Method, endpoint, parameters, headers, response codes, and JSON examples.
