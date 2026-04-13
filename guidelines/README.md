<a id="top"></a>

# FastAPI — Learning Path, API Testing, and Project Roadmap

## Table of Contents

| #   | Section                                                                                                      |
| --- | ------------------------------------------------------------------------------------------------------------ |
| 1   | [Go to the complete repository](#section-1)                                                                  |
| 2   | [Understand FastAPI](#section-2)                                                                             |
| 3   | [Simple example — Calculator API](#section-3)                                                                |
| 4   | [Recommended reading — Read the full document](#section-4)                                                   |
| 4a  | &nbsp;&nbsp;&nbsp;↳ [If you do not have enough time](#section-4)                                             |
| 5   | [How to test an API with Swagger UI](#section-5)                                                             |
| 6   | [Complete API testing guide](#section-6)                                                                     |
| 6a  | &nbsp;&nbsp;&nbsp;↳ [Other optional testing methods](#section-6)                                             |
| 7   | [Practice quiz — API testing](#section-7)                                                                    |
| 7a  | &nbsp;&nbsp;&nbsp;↳ [Answer key](#section-7)                                                                 |
| 8   | [Understand `main.py` line by line](#section-8)                                                              |
| 9   | [Evaluation 2 — Build your own API](#section-9)                                                              |
| 9a  | &nbsp;&nbsp;&nbsp;↳ [Reference project](#section-9)                                                          |
| 10  | [Analyze the front-end of the demo project](#section-10)                                                     |
| 11  | [Evaluation 3 — Build the front-end for your API](#section-11)                                               |
| 11a | &nbsp;&nbsp;&nbsp;↳ [Class project inspiration](#section-11)                                                 |
| 12  | [Next project — Iris AI Platform](#section-12)                                                               |
| 13  | [Suggested order of work](#section-13)                                                                       |
| 14  | [Conclusion](#section-14)                                                                                    |

---

<a id="section-1"></a>

<details>
<summary>1 - Go to the complete repository</summary>

<br/>

Start by visiting the complete repository:

[FastAPI-EN — Complete Repository](https://github.com/inskillflow/FastAPI-EN)

This repository contains the full learning material, practical guides, examples, evaluation instructions, and project references for learning how to build and test APIs with FastAPI.

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="section-2"></a>

<details>
<summary>2 - Understand FastAPI</summary>

<br/>

Begin with this introductory document:

[00 — What is FastAPI](https://github.com/inskillflow/FastAPI-EN/blob/main/00-What-is-FastAPI.md)

This document helps you understand:

- what FastAPI is,
- why it is widely used for modern API development,
- how it simplifies validation and documentation,
- and how it integrates naturally with tools such as Swagger UI.

It is the best starting point before moving to practical examples.

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="section-3"></a>

<details>
<summary>3 - Simple example — Calculator API</summary>

<br/>

Review this simple example first:

[01 — FastAPI Calculator API with Swagger UI](https://github.com/inskillflow/FastAPI-EN/blob/main/01-FastAPI-Calculator-API-with-Swagger-UI.md)

This example shows how to:

- create a very simple FastAPI application,
- define endpoints,
- run the server,
- and test requests using Swagger UI.

It is an excellent first hands-on example before moving to larger projects.

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="section-4"></a>

<details>
<summary>4 - Recommended reading — Read the full document</summary>

<br/>

It is strongly recommended to read the full document below:

[05 — FastAPI APIs: From Concepts to Implementation](https://github.com/inskillflow/FastAPI-EN/blob/main/05-FastAPI-APIs-From-Concepts-to-Implementation.md)

This document provides a broader and more complete view of FastAPI development, from the conceptual foundations to the actual implementation process.

It is preferable to read the entire document carefully before jumping to project work.

---

### If you do not have enough time

Go directly to **point 15** in that document:

**15 — Hands-on Project — Task Manager API (FastAPI + Streamlit)**

Clone the project with:

```bash
git clone https://github.com/inskillflow/demo_api_1_simple_fastapi_app.git
````

This project can serve as a direct practical reference for understanding the overall structure of a FastAPI application connected to a simple front-end.

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="section-5"></a>

<details>
<summary>5 - How to test an API with Swagger UI</summary>

<br/>

To learn how to test an API step by step, read:

[16 — How to Test Your API with Swagger UI — Step by Step for Absolute Beginners](https://github.com/inskillflow/FastAPI-EN/blob/main/16-FastAPI-main-py-Explained-Line-by-Line.md)

This resource explains the testing workflow in a very accessible way. It helps you understand how to:

* open the Swagger UI interface,
* select an endpoint,
* provide input values,
* execute the request,
* and interpret the response returned by the API.

This is an important foundation before doing more complete test scenarios.

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="section-6"></a>

<details>
<summary>6 - Complete API testing guide</summary>

<br/>

For more complete API testing, go to this document:

[06 — FastAPI Method 1: Swagger UI + REST Client — Practical Testing Guide](https://github.com/inskillflow/FastAPI-EN/blob/main/06-FastAPI-Method-1-Swagger-UI-REST-Client-Practical-Testing-Guide.md)

This guide is useful for learning how to test APIs in a more structured and complete way.

It allows you to move beyond simple trial-and-error and understand how to validate endpoints properly.

---

### Other optional testing methods

Documents **07, 08, 09, and 10** present other testing methods.

These optional methods include:

* `curl`
* the **REST Client** extension in VS Code
* **Postman**

You have the freedom to choose the testing method you prefer.

The important point is to understand the API behavior clearly and to be able to test requests and responses properly.

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="section-7"></a>

<details>
<summary>7 - Practice quiz — API testing</summary>

<br/>

Use this quiz to practice:

[01 — Quiz: API Testing with FastAPI](https://github.com/inskillflow/demo_api_1_simple_fastapi_app/blob/main/01-Quiz-API-Testing-with-FastAPI.md)

This quiz is designed to help you strengthen your understanding of API testing concepts in a practical way.

It can be used as preparation for the first evaluation.

---

### Answer key

The correction is available here:

[02 — Answers: Quiz API Testing with FastAPI](https://github.com/inskillflow/demo_api_1_simple_fastapi_app/blob/main/02-Answers-Quiz-API-Testing-with-FastAPI.md)

---

### Evaluation 1

**Evaluation 1** focuses on **how to test an API**, in the form of a **quiz**.

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="section-8"></a>

<details>
<summary>8 - Understand <code>main.py</code> line by line</summary>

<br/>

To understand how a FastAPI application is built step by step, including:

* `Pydantic`
* routes
* request handling
* response handling
* application structure

read this document:

[16 — FastAPI main.py Explained Line by Line](https://github.com/inskillflow/FastAPI-EN/blob/main/16-FastAPI-main-py-Explained-Line-by-Line.md)

This document explains the code line by line and is especially useful for understanding how a real FastAPI application is organized.

It is an important reference before building your own API.

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="section-9"></a>

<details>
<summary>9 - Evaluation 2 — Build your own API</summary>

<br/>

It is now time to build your own API.

The instructions are available here:

[06 — FastAPI Method 1: Swagger UI + REST Client — Practical Testing Guide](https://github.com/inskillflow/FastAPI-EN/blob/main/06-FastAPI-Method-1-Swagger-UI-REST-Client-Practical-Testing-Guide.md)

Do not overthink the project.

Use the already analyzed project as inspiration and make a few changes to produce your own work.

For example, you may:

* change the domain,
* adapt the resources,
* rename the endpoints,
* modify the business logic,
* or use a different dataset or context.

The objective is to build your own API while staying close to a structure you already understand.

---

### Reference project

[demo_api_1_simple_fastapi_app](https://github.com/inskillflow/demo_api_1_simple_fastapi_app/tree/main)

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="section-10"></a>

<details>
<summary>10 - Analyze the front-end of the demo project</summary>

<br/>

This part is intended as front-end practice for the main project.

Study the demo repository:

[demo_api_1_simple_fastapi_app](https://github.com/inskillflow/demo_api_1_simple_fastapi_app)

Then analyze the front-end file:

[frontend.py](https://github.com/inskillflow/demo_api_1_simple_fastapi_app/blob/main/frontend.py)

This will help you understand how a simple interface can interact with a FastAPI backend and how front-end practice can support the development of the larger project.

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="section-11"></a>

<details>
<summary>11 - Evaluation 3 — Build the front-end for your API</summary>

<br/>

After developing your API in point 9, the next step is to build the front-end for that API.

The assignment is available here:

[18 — FastAPI Evaluation 2: Build a Complete REST API](https://github.com/inskillflow/FastAPI-EN/blob/main/18-FastAPI-Evaluation-2-Build-a-Complete-REST-API.md)

As usual, you are encouraged to take inspiration from the class project.

Do not start from zero unless necessary. Reuse what you have already analyzed and adapt it intelligently to your own API.

---

### Class project inspiration

* [Demo project](https://github.com/inskillflow/demo_api_1_simple_fastapi_app)
* [frontend.py](https://github.com/inskillflow/demo_api_1_simple_fastapi_app/blob/main/frontend.py)

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="section-12"></a>

<details>
<summary>12 - Next project — Iris AI Platform</summary>

<br/>

After completing the previous work, move on to the next project:

[iris-ai-platform](https://github.com/inskillflow/iris-ai-platform)

This project represents the next stage of practice and development after the FastAPI and testing workflow covered above.

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="section-13"></a>

<details>
<summary>13 - Suggested order of work</summary>

<br/>

A recommended order is the following:

1. Go to the full repository
   [FastAPI-EN](https://github.com/inskillflow/FastAPI-EN)

2. Understand FastAPI
   [00 — What is FastAPI](https://github.com/inskillflow/FastAPI-EN/blob/main/00-What-is-FastAPI.md)

3. Review the simple calculator example
   [01 — FastAPI Calculator API with Swagger UI](https://github.com/inskillflow/FastAPI-EN/blob/main/01-FastAPI-Calculator-API-with-Swagger-UI.md)

4. Read the main conceptual document
   [05 — FastAPI APIs: From Concepts to Implementation](https://github.com/inskillflow/FastAPI-EN/blob/main/05-FastAPI-APIs-From-Concepts-to-Implementation.md)

5. Learn API testing
   [06 — Practical Testing Guide](https://github.com/inskillflow/FastAPI-EN/blob/main/06-FastAPI-Method-1-Swagger-UI-REST-Client-Practical-Testing-Guide.md)

6. Complete the quiz
   [01 — Quiz](https://github.com/inskillflow/demo_api_1_simple_fastapi_app/blob/main/01-Quiz-API-Testing-with-FastAPI.md)

7. Review the answer key
   [02 — Answers](https://github.com/inskillflow/demo_api_1_simple_fastapi_app/blob/main/02-Answers-Quiz-API-Testing-with-FastAPI.md)

8. Understand `main.py` line by line
   [16 — main.py explained](https://github.com/inskillflow/FastAPI-EN/blob/main/16-FastAPI-main-py-Explained-Line-by-Line.md)

9. Build your own API
   [Demo project](https://github.com/inskillflow/demo_api_1_simple_fastapi_app/tree/main)

10. Analyze the front-end
    [frontend.py](https://github.com/inskillflow/demo_api_1_simple_fastapi_app/blob/main/frontend.py)

11. Build the front-end for your own API
    [Evaluation instructions](https://github.com/inskillflow/FastAPI-EN/blob/main/18-FastAPI-Evaluation-2-Build-a-Complete-REST-API.md)

12. Continue with the next project
    [iris-ai-platform](https://github.com/inskillflow/iris-ai-platform)

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="section-14"></a>

<details>
<summary>14 - Conclusion</summary>

<br/>

This roadmap provides a structured progression for learning FastAPI, understanding API testing, analyzing a complete demo project, and then building your own API and front-end.

The work is organized in a practical sequence:

* understand the framework,
* study a simple example,
* learn how to test,
* complete a quiz,
* analyze the source code,
* build your own API,
* build the front-end,
* and then move on to the next project.

This approach allows you to move from guided understanding to independent development in a progressive and concrete way.

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>
