#  Step CI Workflow Syntax

## Versions

- 1.0 (current)

## YAML

Step CI workflows use YAML syntax. You can learn more about YAML from ["Learn YAML in Y minutes"](https://learnxinyminutes.com/docs/yaml/)

## Syntax

### `version`

Required. Step CI workflow file version

### `name`

Required. Workflow name

### `env`

Optional. Environment Variables

**Example: Defining environment variables**

```yaml
env:
  host: example.com
```

**Example: Using environment variables in steps**

```yaml
env:
  host: example.com
steps:
  - name: GET request
    url: https://{{env.host}}
    method: GET
```

### `steps`

Required. List of steps to be executed by runner

### `steps.<step>`

Required. The step to be executed

**Example: Checking Response Status**

```yaml
steps:
  - name: GET request
    url: https://example.com
    method: GET
    check:
      status: 200
```

### `steps.<step>.id`

Optional. Step id

### `steps.<step>.name`

Required. Step name

### `steps.<step>.url`

Required. Request URL

### `steps.<step>.method`

Required. Request method

### `steps.<step>.headers`

Optional. Request headers

**Example: Setting Request Headers**

```yaml
steps:
  - name: Request
    url: https://echo.hoppscotch.io/graphql
    method: POST
    headers:
      Content-Type: application/json
```

### `steps.<step>.body`

Optional. Request Body

**Example: Setting Request Body**

```yaml
steps:
  - name: Post the post
    url: https://jsonplaceholder.typicode.com/posts
    method: POST
    headers:
      Content-Type: application/json
    body: |
      {
        "title": "Hello Step CI!",
        "body": "This is the body",
        "userId": 1
      }
```

### `steps.<step>.json`

Optional. Request JSON

**Example: Setting Request JSON**

```yaml
steps:
  - name: Post the post
    url: https://jsonplaceholder.typicode.com/posts
    method: POST
    headers:
      Content-Type: application/json
    json:
      title: Hello Step CI!
      body: This is the body
      userId: 1
```

### `steps.<step>.form`

Optional. Form submission

**Example: Submitting Form**

```yaml
steps:
  - name: Subscribe to newsletter
    url: http://newsletter.com
    method: POST
    form:
      email: hello@stepci.com
```

### `steps.<step>.graphql`

Optional. GraphQL Data

**Example: Sending GraphQL request**

```yaml
steps:
  - name: Request
    url: https://echo.hoppscotch.io/graphql
    method: POST
    headers:
      Content-Type: application/json
    graphql:
      query: |
        query Request {
          method
          url
          headers {
            key
            value
          }
        }
      variables:
        id: 1
```

### `steps.<step>.captures`

Optional. Capture response values into named variables

**Example: Submitting post and using captured id**

```yaml
steps:
  - name: Post the post
    url: https://jsonplaceholder.typicode.com/posts
    method: POST
    headers:
      Content-Type: application/json
    body: |
      {
        "title": "Hello Step CI!",
        "body": "This is the body",
        "userId": 1
      }
    captures:
      - name: id
        jsonpath: $.id
  - name: Get post by id
    url: https://jsonplaceholder.typicode.com/posts/{{captures.id}}
    method: GET
    headers:
      Content-Type: application/json
```

Following capture types are available to you

- `jsonpath` - JSONPath
- `xpath` - XPath
- `header` - Response Header
- `selector` - Cheerio (HTML) selector

### `steps.<steps>.check`

Optional. Provide checks to validate responses

Available checks:

- `status` - Status code
- `statusText` - Status text
- `headers` - Response headers
- `body` - Response body
- `duration` - Request duration
- `jsonpath` - JSONPath
- `xpath` - XPath
- `selector` - Cheerio (HTML) selector

**Example: Checking status code**

```yaml
steps:
  - name: GET request
    url: https://example.com
    method: GET
    check:
      status: /^20/
```

**Example: Checking headers**

```yaml
steps:
  - name: Get post by id
    url: https://jsonplaceholder.typicode.com/posts
    method: GET
    headers:
      Content-Type: application/json
    check:
      headers:
        Content-Type: application/json; charset=utf-8
```

**Example: Checking JSON Response**

```yaml
steps:
  - name: GET request
    url: https://jsonplaceholder.typicode.com/posts/1
    method: GET
    check:
      jsonpath:
        $.id: 1
```

**Example: Checking XML Response**

```yaml
steps:
  - name: GET request
    url: https://api-campaign-us-1.goacoustic.com/XMLAPI
    method: GET
    check:
      xpath:
        //SUCCESS: "false"
```

### `steps.<step>.if`

Optional. Condition. For Syntax, see [Filtrex Documentation](https://github.com/joewalnes/filtrex#expressions)

**Example: Conditional Request**

```yaml
steps:
- name: GET request
  url: https://example.com
  method: GET
  check:
    status: 200
  captures:
  - name: title
    selector: title
- name: GET request
  url: https://example.com
  if: title == "Example Domain"
  method: GET
  check:
    status: 200
```

## Using Matchers

Matchers are useful when you want to check whether values match patterns

**Example: Response duration to be lower or equal 500ms**

```yaml
steps:
  - name: GET Request
    url: https://example.com
    method: GET
    check:
      duration:
        - lte: 500
```

The matchers can be chained together

**Example: Chaining matchers**

```yaml
steps:
  - name: GET Request
    url: https://example.com
    method: GET
    check:
      duration:
        - lte: 500
        - gte: 300
```

Available Matchers:

- `eq` - Equal (===)
- `ne` - Not equal (!==)
- `gt` - Greater than (>)
- `gte` - Greater than or equal (>=)
- `lt` - Lower than (<)
- `lte` - Lower than or equal (<=)
- `in` - Includes
- `nin` - Not includes
- `match` - Regex pattern

Boolean Matchers:

- `isNumber` - Is Number
- `isString` - Is String
- `isBoolean` - Is Boolean
- `isNull` - Is Null
- `isDefined` - Is Defined
- `isObject` - Is Object
- `isArray` - Is Array