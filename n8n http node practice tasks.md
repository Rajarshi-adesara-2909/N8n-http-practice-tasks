# n8n HTTP Request Node - Practice Tasks & Hints

Complete these tasks in n8n to master the HTTP Request node. 
- **Local Access**: `http://localhost:3005`
- **Network Access**: Use the IP address shown in your terminal (e.g., `http://192.168.x.x:3005`) if testing from another computer.

---

## 🟢 Easy Level (Basic Requests)
*Focus: Simple data retrieval and basic configuration.*

1. **Get All Data**: Create a workflow to fetch all rows from `/data`.
   > **Hint**: Select **GET** method and enter the URL. Ensure "JSON" is selected as the response format.
2. **Fetch Specific Row**: Get only the data for the first row (`/data/0`).
   > **Hint**: Append `/0` to the end of your URL.
3. **Limit Results**: Use a Query Parameter `limit=10` to get only 10 rows.
   > **Hint**: Enable **Send Query Parameters** and add a parameter named `limit` with value `10`.
4. **Metadata Check**: Use the `HEAD` method on `/data` and check the `X-Total-Count` header in the output.
   > **Hint**: Select **HEAD** as the method. n8n will return only headers; check the "Output" tab for the header name.
5. **Methods Discovery**: Use the `OPTIONS` method on `/data` to see which HTTP methods are allowed.
   > **Hint**: Change the method to **OPTIONS**. Check the `allow` header in the response.
6. **Fetch Last Row**: Figure out how to fetch the very last row in the system.
   > **Hint**: Use a **HEAD** request first to find the `X-Total-Count`, then use that number minus 1 in a GET request (e.g., `/data/120`).
7. **Basic Connection**: Set up a workflow that simply pings the server (GET `/`) and returns a success message.
   > **Hint**: The server doesn't have a specific `/` route defined, but it will return a 404. Look at the "Response" tab in the node settings to see how n8n handles the status code.

---

## 🟡 Medium Level (Data Manipulation & Headers)
*Focus: Modifying data and using headers.*

1. **Search by Name**: Use a Query Parameter `name=real` to find specific real estate entries.
   > **Hint**: Add a query parameter `name` with the string you want to search for.
2. **Add a Record**: Use `POST` to send a JSON body and add a new "fake" property to the list.
   > **Hint**: Select **POST** method, enable **Send Body**, choose **JSON**, and paste a JSON object like `{"Name": "Test"}`.
3. **Partial Update**: Use `PATCH` to change ONLY the phone number of the row at index `5`.
   > **Hint**: Use method **PATCH**, target URL `/data/5`, and send a body with only the phone field.
4. **Full Replace**: Use `PUT` to completely overwrite row `10` with a new JSON object.
   > **Hint**: Use method **PUT**. Remember, PUT usually overwrites the entire entry, so any fields you don't send might be lost!
5. **Delete Record**: Use `DELETE` to remove row `15` from the list.
   > **Hint**: Select method **DELETE** and target `/data/15`.
6. **Custom Header**: Send a `POST` request with a custom header `X-Category: residential` and verify it in the server terminal logs.
   > **Hint**: Enable **Send Headers** and add a header with your custom name and value.
7. **JSON Parsing**: Fetch data and use an n8n **Set** node to extract only the "Name" and "Phone" fields from the result.
   > **Hint**: Connect a **Set** node after the HTTP Request. Use "Add Value" -> "String" and use expressions like `{{ $json.results[0].Name }}`.

---

## 🟠 Hard Level (Authentication & Logic)
*Focus: Security, filtering, and workflow logic.*

1. **Secure Access**: Try to GET `/secure-data` without auth, see it fail, then add the `x-api-key`.
   > **Hint**: Use **Header Auth** in the "Authentication" section or manually add it in **Send Headers** as `x-api-key`.
2. **Filter & Count**: Fetch all data where the name includes "Los Angeles" and count them in n8n.
   > **Hint**: Use the `results` array from the response and pass it to a **Filter** node, then check the length of the result array.
3. **Sequential Update**: Fetch a row, change one field, and then `PATCH` it back to the server in the same workflow.
   > **Hint**: HTTP Request (GET) -> Set (Update field) -> HTTP Request (PATCH).
4. **Error Handling**: Use the **Options** tab in n8n to set "Ignore Errors" to true, and handle a 404 error (e.g., fetch `/data/9999`) using an **If** node.
   > **Hint**: Under **Options**, toggle **Never Error**. Then use an **If** node to check if `{{ $response.status }}` is 404.
5. **Dynamic ID**: Set up a workflow that uses an expression (e.g., from a previous node) to choose which ID to fetch.
   > **Hint**: Use a **Set** node to define an `id` variable, then in the URL of the HTTP node, use `http://localhost:3005/data/{{ $json.id }}`.
6. **Header Analysis**: Send a request and use an n8n node to display "Total Rows" based on the `X-Total-Count` header received.
   > **Hint**: The headers are available in the output under `{{ $headers['x-total-count'] }}`.
7. **Bulk Add**: Use a **Split In Batches** node in n8n to send 5 separate `POST` requests to the backend.
   > **Hint**: Create an array of 5 items, use **Split In Batches** with batch size 1, and place the HTTP Request inside the loop.

---

## 🔴 Extra Hard Level (Complex Flows & Orchestration)
*Focus: Advanced n8n techniques and state management.*

1. **The "Cleaner"**: Fetch all rows, find any that have a missing phone number, and `PATCH` them with "No Phone Provided".
   > **Hint**: Use a **Filter** or **If** node inside a loop to check if the Phone field is empty.
2. **Auth Switch**: Build a workflow that tries to access the secure endpoint; if it fails (401), it sends an alert/message to you.
   > **Hint**: Set "Never Error" to true, then use an **If** node to branch on status 401.
3. **Data Sync Simulation**: Add 10 items via `POST`, then immediately `GET` the list and verify the count increased by 10.
   > **Hint**: Use a **Set** node to store the initial count, perform the boots, then a final GET to compare.
4. **The Search Loop**: Filter for "Real", then for each result found, perform a `GET` on its specific ID to get full details.
   > **Hint**: This involves a loop (Split in Batches) where the URL changes dynamically for each item index.
5. **Conditional Update**: Only `PATCH` a record if its name contains a specific keyword, otherwise `DELETE` it.
   > **Hint**: Branch your workflow using an **If** node based on the Name field.
6. **Header Routing**: Send a request with a header `X-Action: update-only`. Configure the backend (extra credit!) or build n8n logic to only proceed if that header is present.
   > **Hint**: In n8n, use a **Conditional** or **Filter** node to check the value of an incoming property that you've set as a header.
7. **Performance Test**: Set up a **Wait** node and a loop to spam 50 `GET` requests; monitor the server terminal for any slowdowns or log overflows.
   > **Hint**: Use a loop with a counter. Add a **Wait** node of 100ms to avoid overwhelming your own CPU!
