# Cucumber-export

This cucumber formatter works well with cucumber versions from 6.x inclusive

## Installation

```sh
npm install @restqa/cucumber-export
```

## API

```js
const { getFormatter } = require('@restqa/cucumber-export');
```

The `getFormatter` object expose a function receving your exports options.
All The options are shared below.

### cucumberExport.getFormatter([options])

Returns a cucumber formatter where the result will be pre-formatted and transferred
to the selected destination.

#### Options

The Options are mandatory.

##### uuid \<string> (optional)

Represent the unique identifier of current test suit exported.

_Default: autogenerated from [uuid : v4](https://www.npmjs.com/package/uuid#version-4-random)_

##### name \<string> (optional)

Represent the name of the current test suite (example: The backend api for the mobile application)

##### startTime \<date> (optional)

Represent a unique key of the current test suite (example: 2018–01–30T12:34:56+00:00)

_Default: current datetime_

##### key \<string> (optional)

Represent a unique key of the current test suite (example: backend-api)

##### env \<string> (optional)

Represent a environment of the current test suite (example: uat)

##### outputs \<array> (required)

You can configure different output, the available output reporters are :

###### File

Export the result to a JSON file

```
{
  type: 'file',
  enabled: true,
  config: {
    path: 'my-report.json' // File to save
  }
}
```

###### Http

Export the result to a http result

```
{
  type: 'http',
  enabled: true,
  config: {
    url: 'https://httpdump.io/rb6zi', // The http endpoint to send the result
    method: 'POST', // The http method to use
    headers: { // The request headers to use
      apikey: 'xxx-yyy-zzz'
    }
  }
}
```

###### Slack

Receive a notification on slack about you test report

```
{
  type: 'slack',
  enabled: true,
  config: {
    url: 'https://hooks.slack.com/service/xxx/yyy/zzz', // The slack webhook url
    onlyFailed: true, // Trigger the hook only for test failure  (default: false)
    showErrors: true,  // Show the error message within slack
    reportUrl: 'https://www.test.report/{uuid}' // The url to access to your detail test report if you have one
  }
}
```

_Example_:

![slack notification](https://restqa.io/assets/img/utils/cucumber-export-slack.png)

###### Microsoft Teams

Receive a connector card in your Microsoft Teams channel when your test finishes

```
{
  type: 'microsoft-teams',
  enabled: true,
  config: {
    url: 'https://outlook.office.com/webhook/xxx/IncomingWebhook/yyy/zzz', // The teams webhook url (tutorial : https://docs.microsoft.com/en-us/learn/modules/msteams-webhooks-connectors/5-exercise-incoming-webhooks)
    onlyFailed: true, // Trigger the hook only for test failure  (default: false)
    showErrors: true,  // Show the error message within teams
  }
}
```

In order to get the url of the incoming webhook, take a look at : https://docs.microsoft.com/en-us/learn/modules/msteams-webhooks-connectors/5-exercise-incoming-webhooks

_Example_:

![teams notification](https://restqa.io/assets/img/utils/cucumber-export-teams.png)

###### Line

Receive a notification on line about you test report

```
{
  type: 'line',
  enabled: true,
  config: {
    token: 'sEdkjfEr745aasd546saSDdjklawE74S', // The line notfication token (get your token here : https://notify-bot.line.me/en/)
    onlyFailed: true, // Trigger the hook only for test failure  (default: false)
    reportUrl: 'https://www.test.report/{uuid}' // The url to access to your detail test report if you have one
  }
}
```

In order to get the token for the nofify line app, take a look at : https://notify-bot.line.me/en/


_Example_:

![line notification](https://restqa.io/assets/img/utils/cucumber-export-line.jpg)

###### Elastic-Search

Export the result to an elastic search server (using rolling index)

```
{
  type: 'elastic-search',
  enabled: true,
  config: {
    url: 'http://my-elastic-search.local:9200', // The elastic search endpoint
    index: 'bdd-e2e'  // The elastic search index to use (default: restqa-e2e-result)
  }
}
```

###### Http html Report

(Note: This modules only exports data to various remote endpoints, it doesn't generate any html)

Export the result to a remote endpoint in order to generate an html report.

For more information about the generation of the report you can look at the project : https://github.com/restqa/cucumber-html-reporter-server
Basically you have 2 options to use this reporter.]:

1. Use the Saas version hosted on : html-report.restqa.io (pro: ready, con: data privacy, shared)
2. Host your own, sotfware available here : https://github.com/restqa/cucumber-html-reporter-server

```
{
  type: 'http-html-report',
  enabled: true,
  config: {
    url: 'https://html-report.your-domain.dev' // (default : https://html-report.restqa.io)
  }
}
```

## Usage

### Setup your formatter

Create a new file at the root of your project. (restqa-formatter.js)

```js
# <root-dir>/restqa-formatter.js

const { getFormatter } = require('@restqa/cucumber-export')

let envConfig = {
  uuid: 'xxx-yyy-zzz',
  name: 'local',
  env: 'uat',
  outputs: [
    {
      type: 'http-html-report',
      enabled: true,
      config: {
        url: 'https://html-report.your-domain.dev' // (default : https://html-report.restqa.io)
      }
    },
    {
      type: 'http',
      enabled: true,
      config: {
        url: 'https://httpdump.io/lb8f7',
        method: 'POST',
        headers: {
          'x-apikey': 'xxx-yyy-zzz'
        }
      }
    },
    {
      type: 'elastic-search',
      enabled: true,
      config: {
        url: 'http://my-elastic-search.local:9200',
        index: 'bdd-e2e'
      }
    },
    {
      type: 'file',
      enabled: true,
      config: {
        path: 'my-report.json' // File to save
      }
    },
    {
      type: 'slack',
      enabled: true,
      config: {
        url: 'https://hooks.slack.com/service/xxx/yyy/zzz', // The slack webhook url
        onlyFailed: true, // Trigger the hook only for test failure  (default: false)
        showErrors: true,  // Show the error message within slack
        reportUrl: 'https://www.test.report/{uuid}' // The url to access to your detail test report if you have one
      }
    },
    {
      type: 'microsoft-teams',
      enabled: true,
      config: {
        url: 'https://outlook.office.com/webhook/xxx/IncomingWebhook/yyy/zzz', // The teams webhook url
        onlyFailed: true, // Trigger the hook only for test failure  (default: false)
        showErrors: true,  // Show the error message within teams
        reportUrl: 'https://www.test.report/{uuid}' // The url to access to your detail test report if you have one
      }
    },
    {
      type: 'line',
      enabled: true,
      config: {
        token: 'sEdkjfEr745aasd546saSDdjklawE74S', // The line notfication token
        onlyFailed: true, // Trigger the hook only for test failure  (default: false)
        reportUrl: 'https://www.test.report/{uuid}' // The url to access to your detail test report if you have one
      }
    }
  ]
}

module.exports = getFormatter(envConfig)
```

### Run cucumber-js

You can now run cucumber-js with the just created formatter

`cucumber-js -f ./restqa-formatter.js:restqa.log`

> It's important to defined formatter export path to have access the logs, you can refer to the cucumber-js documentation (https://github.com/cucumber/cucumber-js/blob/master/docs/cli.md#formats)'

## License

[MIT License](./LICENSE)
