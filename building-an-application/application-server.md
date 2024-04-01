# Application server

So we've got our `FastAPI` application up and running. We've built the bare bones of our first endpoint (which we're not done with yet). And we've also written some tests to hit our endpoint as well as against the underlying business logic.

***

## OpenAPI specification

Wouldn't it be great if we could visualise our API in a standardised way?

This is where the OpenAPI specification comes into play. You might have heard this being refered to as 'swagger' docs.  The OpenAPI specification is a standardised way of presenting REST APIs. Specifications can be written in YAML or JSON, they are consumed and then presented back to us in the OpenAPI format.

We've also not written any such specification.

Luckily for us, our chosen framework has generated it on our behalf based on our API code already!

***

## Starting the server

So without further ado, lets drop into the terminal and start the server:

```python
uvicorn main:app --reload
```

Make sure that your virtual environment is activated before running the above command.

Your terminal will output something that looks like the following after running the `uvicorn` command:

```sh
❯ uvicorn main:app --reload
INFO:     Will watch for changes in these directories: ['<directory where your project is located>']
INFO:     Uvicorn running on http://127.0.0.1:8000 (Press CTRL+C to quit)
INFO:     Started reloader process [5140] using WatchFiles
INFO:     Started server process [5142]
INFO:     Waiting for application startup.
INFO:     Application startup complete.
```

This means that the application is now being served at the local host address `https://127.0.0.1` on port `8000`.

***

## Seeing the auto-generated API documentation

So now if we head over to the following address in a browser, we will see the auto-generated OpenAPI documentation for our API:

```sh
http://127.0.0.1:8000/docs
```

This will look something like the following:

<figure><img src="../.gitbook/assets/Screenshot 2024-04-01 at 20.25.11.png" alt=""><figcaption></figcaption></figure>

This is our API created with the OpenAPI format. Each panel represents an individual endpoint. In our case we just have the 1 single endpoint.&#x20;

If we take compare this with our API code:

{% code lineNumbers="true" %}
```python
@app.get(path="/income-taxes")
def calculate_income_taxes(salary: float) -> dict[str, float]:
    tax_owed: float = calculate_income_tax_owed(salary=salary)
    return {"tax owed": tax_owed}
```
{% endcode %}

We can see that on line 1 our `/income-taxes` is used from the `path` argument we provided to the `app` decorator.&#x20;

On line 2, the name of the function forms the generated description which is associated with the endpoint.

If we open up that endpoint in our browser, we can see the expanded tab:

<figure><img src="../.gitbook/assets/Screenshot 2024-04-01 at 20.34.07.png" alt=""><figcaption></figcaption></figure>

The `salary` argument has also been rendered as a query parameter to our endpoint. And to top it off, the generated documentation even knows to only take `number` type values because of how we type hinted the `salary` argument.

This has the added benefit that there is only 1 single source of truth. Our code. We don't have to maintain a seperate documentation entity in the form of a YAML file or JSON somewhere else that would eventually and quite easily get out of sync. This also reduces our maintenance burden.



