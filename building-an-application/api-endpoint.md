# API Endpoint

To begin with we are going to need an HTTP API endpoint that our users can send a request to. This endpoint should return the calculated income tax that they owe based on the information they provided in their request to the endpoint.

For now we're going to keep things nice and simple and assume that there are no other sources of income to be considered. So this will be a pretty simple calculation of, given a salary figure how much of that figure will be owed in income tax.&#x20;

Note that this will be for UK income tax calculations. If you are following along from a different jurisdiction you can just adjust the calculation to suit your needs.
