A cloud function is a single unit of code that can be executed in response to a specific event. In the context of serverless architecture, cloud functions are a way of deploying and running small pieces of code without the need for managing infrastructure. This can greatly simplify the process of building and deploying applications. So, let's learn how to deploy a simple function in Google Cloud Platform.

Every function must have a trigger, which is the specific event that calls the function and makes our application "wake up". 
We can have event triggers or HTTP triggers, but I choose to use HTTP triggers for this tutorial.

### How to do it, step by step
First, you need to have Google SDK installed in your machine. Once you have it, authenticate to your account using

`gcloud auth login`

Once you have this basic setup, let's start.

### Writing a cloud function
Let's use a javascript function here. I choose to use a **HTTP trigger**. It means that **our function will be triggered every time we receive a HTTP request to the URL assigned to it**.

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
    --region=us-central1 \
    --trigger-http \
    --allow-unauthenticated
```

The important part is the `entry-point` flag. It specifies **which function** is the entry point. Google will lookup in our `index.js` to find it.

Another interesting flag is the `allow-unauthenticated`. Without this flag, we would only be able to invoke our function if we made a request using a token from an account that has the `roles/cloudfunction.functions.invoker` permission.

**Note:** to successfully use this flag, you need to have the `cloudfunctions.functions.setIamPolicy` permission, since what `--allow-unauthenticated` does is basically to set a IAM policy for your cloud function, enabling allUsers to invoke it.

So, to deploy, we run `./deploy.sh`. In the output of the commands, you'll se the URL that GCP assigned to your function.

Go to it using your browser, and you'll be able to see "Hello World" printed in your screen.
