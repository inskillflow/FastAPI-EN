# fastapi-en

<details>
<summary>What is FastAPI and why use it in an ML or AI pipeline?</summary>

<br/>

FastAPI is a modern Python framework used to build APIs quickly, efficiently, and with high performance. It is designed for backend development and is especially useful when applications need to send and receive data through HTTP requests.

In a machine learning or artificial intelligence pipeline, FastAPI is often used to expose a trained model as a service. Instead of keeping the model inside a notebook or a local script, FastAPI allows developers to create endpoints that receive input data, run predictions, and return results in a structured format such as JSON.

It is particularly useful because it separates the backend logic from the user interface. For example, a model may be trained using TensorFlow, PyTorch, or scikit-learn, then deployed with FastAPI so that other systems such as Streamlit, web applications, mobile apps, or external services can interact with it.

FastAPI is also valuable because it is fast, lightweight, and easy to structure for production-ready systems. It supports request validation, type hints, automatic documentation, and clean routing. This makes it easier to build reliable AI services that are easier to test, maintain, and scale.

Another important advantage is that FastAPI automatically generates interactive API documentation through Swagger UI and ReDoc. This helps developers test endpoints directly in the browser and makes the API easier to understand and use.

In an ML or AI pipeline, FastAPI is commonly used for model inference, model serving, connecting the frontend to the backend, integrating AI services into larger systems, and deploying prediction endpoints for real-time use.

In short, FastAPI is used in an ML or AI pipeline because it provides a fast and structured way to turn a machine learning model into an accessible backend service that other applications and users can interact with.

</details>
