# fastify-zipkin

[![js-standard-style](https://img.shields.io/badge/code%20style-standard-brightgreen.svg?style=flat)](http://standardjs.com/)  [![Build Status](https://travis-ci.org/fastify/fastify-zipkin.svg?branch=master)](https://travis-ci.org/fastify/fastify-zipkin)

Fastify plugin for Zipkin distributed tracing system.

## Install
```
npm i fastify-zipkin --save
```

## Usage
Require the plugin and register it within Fastify, the pass the following options: `{ tracer [, serviceName] [, port] }`
```js
const fastify = require('fastify')()

const { Tracer, BatchRecorder, jsonEncoder: { JSON_V2 } } = require('zipkin')
const { HttpLogger } = require('zipkin-transport-http')
const CLSContext = require('zipkin-context-cls')

const zipkinBaseUrl = 'http://localhost:9411'
const recorder = new BatchRecorder({
  logger: new HttpLogger({
    endpoint: `${zipkinBaseUrl}/api/v2/spans`,
    jsonEncoder: JSON_V2
  })
})

const ctxImpl = new CLSContext('zipkin')
const localServiceName = 'service-name'
const tracer = new Tracer({ ctxImpl, recorder, localServiceName })

fastify.register(require('fastify-zipkin'), { tracer })

fastify.get('/', (req, reply) => {
  reply.send({ hello: 'world' })
})

fastify.listen(3000, err => {
  if (err) throw err
  console.log('Server listenting on localhost:', fastify.server.address().port)
})
```

## License

Licensed under [MIT](./LICENSE).
