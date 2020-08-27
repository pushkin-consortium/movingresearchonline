**Compatibility for pushkin-sitetemplate-default v1.2.0 with pushkin-cli v3.0.0**

In `pushkin-sitetemplate-default` v.2.0.0, we made some changes to the configuration files to improve security and allow for future upgrades. v3+ of the CLI assumes these changes. To use the new CLI and to benefit from these changes, please make the following quick adjustments:

## A.
1. Make a temporary copy of `pushkin/front-end/src/config.js` with the [New version].

2. Replace `pushkin/front-end/src/config.js` with the [New version]

3. Make a temporary copy of `pushkin.yaml`.

4. Replace `pushkin.yaml` with [New Version]

5. Restore any customization you had made to `pushkin.yaml` by comparing your temporary copy with the new version. Most likely, the only customization you would have done is changing `DockerHubID`.

6. Look at your temporary copy of `config.js`. You will see that the settings in `config.js` have all moved to the `config` section of `pushkin.yaml`. Please edit `pushkin.yaml` to reflect any such customization. For instance, if you changed your email from `me@mydomain.com` to an actual email address, update it.

##B.
For each experiment:

* Open `experiments/[YOUR EXPERIMENT]/web page/src/index.js`. On line 40, you should see:

```
await pushkin.connect('/api/[YOUR EXPERIMENT]');
```

Replace this with 

```
await pushkin.connect(this.props.api);
```

and save.

* Go to `[path-to-experiment]/api controllers/`

Run:

```
$ yarn upgrade pushkin-api --latest
```

* Open `[path-to-experiment]/worker/start.sh`. Replace everything with:

```
#!/bin/bash
echo "Worker started. Waiting for rabbitMQ"
AMQP_DOMAIN=$(echo $AMQP_ADDRESS | sed -e "s/[^/]*\/\/\([^@]*@\)\?\([^:/]*\).*/\2/")
echo "Waiting for port 5672 on $AMQP_DOMAIN"
while ! nc -z $AMQP_DOMAIN 5672; do sleep 3; done
echo "Rabbitmq loaded"
node index.js

```

## C.
1. Open `pushkin/front-end/src/components/Quizzes/TakQuiz.js`. Add a new line at the very top:

```
import { CONFIG } from '../../config';
```

Then, on line 24, you should see:

```
<QuizComponent {...this.props} />
```

replace this with

```
        <QuizComponent {...this.props} api={CONFIG.apiEndpoint.concat('/').concat(match.params.quizName)} />

```

and save.

2. Go to `pushkin/api`. Run:

```
$ yarn upgrade pushkin-api --latest
```

## D.

1. Run `$ pushkin setup-transaction-db`
