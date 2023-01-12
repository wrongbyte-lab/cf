## Deploying a function on Google Cloud
We are using a serverless function, where our software runs once it has a "trigger" to "wake up". How? Using a function.
Every function must have a trigger on Google Cloud. Therefore, we have the following possible triggers:
- Event-driven functions
    - Background functions
    - CloudEvent functions
- HTTP functions

### How to do it, step by step
First, you need to have Google SDK installed in your machine. Once you have it, authenticate to your account using

`gcloud auth login`

Once you have this basic setup, let's start.

### Writing a cloud function
Let's use a javascript function here. I choose to use a **HTTP trigger**. It means that **our function will be triggered every time we receive a HTTP request to its URL**.

> By default, Cloud Functions attempts to load source code from a file named index.js at the root of your function directory. To specify a different main source file, use the main field in your package.json file.

As we are dealing with HTTP triggers, we must create a simple function that runs every time we receive a request:
```js
const myFunction = async (req, res) => {
  const name = req.query.name || "World";
  res.send(`Hello ${name}!`);
};

module.exports = {
    myFunction
}

```

This is our `index.js`: nothing but a simple function. Our code here doesn't need to have a complicated structure, but it must have a **function entry point**, which is the code that is executed when the Cloud Function is invoked.

### Deploying

In the code below, 
```sh
gcloud functions deploy $FN_NAME \
    --entry-point=$FN_ENTRY_POINT \
    --runtime=nodejs16 \
    --source=. \
    --region=us-central1 \
    --trigger-http \
    --allow-unauthenticated
```

The important part is the `entry-point` flag. It specifies **which function** is the entry point. Google will lookup in our `index.js` to find it.

Another interesting flag is the `allow-unauthenticated`. Without this flag, we would only be able to invoke our function if we made a request using a token from an account that has the `roles/cloudfunction.functions.invoker` permission.

**Note:** to successfully use this flag, you need to have the `cloudfunctions.functions.setIamPolicy` permission, since what `--allow-unauthenticated` does is basically to set a IAM policy for your cloud function, enabling allUsers to invoke it.
