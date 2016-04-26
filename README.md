Boilerplate for creating a new lambda deployable in RiffRaff

* Notice: * This is my opinionated<sup>*</sup> way of doing it, you might not like it

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
* Create AWS keys with S3 write access to the buckets used by RiffRaff.
* Run

```
travis encrypt --add AWS_ACCESS_KEY_ID=your_key
travis encrypt --add AWS_SECRET_ACCESS_KEY-your_secret
```

### Deploy in RiffRaff

*
