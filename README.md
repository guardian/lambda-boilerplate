# **THIS REPO IS NO LONGER MAINTAINED**
If you use it, note it uses an unsupported riff-raff deploy file.

Please use [this repo](https://github.com/guardian/fulfilment-node-stub-lambda) for a more up to date boilerpate.

___________________________________________________________

Boilerplate for creating a new lambda deployable in RiffRaff

**Notice:** This is my opinionated<sup>*1</sup> way of doing it, you might not like it

### Create a new lambda

* Download [this project as a ZIP](https://github.com/guardian/lambda-boilerplate/archive/master.zip).
* Create the S3 bucket where RiffRaff uploads artifacts on deploy, e.g. `lambda-boilerplate-dist`.
* Adjust the `Parameters` of CloudFormation script in `conf/cloudformation.yml`.
* Run

```
npm install
gulp cfn archive
```

* The previous command compiles `cloudformation.yml` into JSON and generates a sample Lambda package.
* Inside the dist bucket (`lambda-boilerplate-dist`) create a `CODE` folder and upload `artifact.zip` from `tmp/riffraff/packages/static/artifact.zip`.
* Create a new CloudFormation stack using `tmp/riffraff/packages/cloudformation/couldformation.json`
* Copy the name of the newly generated Lambda function and put it inside `conf/deploy.yml` in `lambda.data.functions.STAGE.name`
* Repeat for `PROD` stage if necessary.


### Continuous integration

* Create a new GitHub repository and push the code so far.
* Log in in [Travis](https://travis-ci.org/) and add a new repository. You'll find your repository below `The Guardian`. I recommend changing the settings and set
   * `On` to `Build only if .travis.yml is present`
   * `On` to `Build pushes`
   * `Off` to `Limit concurrent jobs`
   * `Off` to `Build pull requests`
* Create AWS keys with S3 write access to the buckets used by RiffRaff. Make sure the permission is as tightly scoped as possible.
* Remove `env.global.secure` from `.travis.yml`
* Run

```
travis encrypt AWS_ACCESS_KEY_ID=your_key --add
travis encrypt AWS_SECRET_ACCESS_KEY=your_secret --add
```

* Adjust the values of `package.json`. You want to update at least `name`, `projectName` and `description`.

* Commit and push, it should run your first build in Travis and upload the artifact in RiffRaff


### Deploy in RiffRaff

* If your build is successful, you should be able to use RiffRaff just like any other deploy.


### Unit tests

I like using this style of dependency injection in lambda:

```js
const s3instance = AWS.S3();

export default function handler (events, context, callback) {
	handleEvents({events, callback, s3: s3instance});
}

export function handleEvents ({events, callback, s3}) {
	// do your things
}
```

You can create your actual instances outside the handler, but your handler method is simply forwarding everything to `handleEvents`. In your test files you can call directly `handleEvents` with all your mocks / stubs/ spies.

* `npm test` to run your tests once.
* `nodemon --exec 'npm test' --ignore tmp` to watch your files and run tests on save.
