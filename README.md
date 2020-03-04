# Local Dev

Pros and cons of various modes of local development when working on a web app
which talks to an API on a different domain.

## `FILE` Protocol

Just open `index.html` with your browser.

Pros:

- Extremely easy and portable, just `open index.html` or `start index.html`
- No tooling or dependencies needed, works out of the box
- Really fast as there is only disk I/O but no network I/O

Cons:

- In Firefox, `fetch` cannot be used, because of CORS, specifically this change:
  https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS/Errors/CORSRequestNotHttp
  - Scripts with `type=module` also cannot be used because the browser won't fetch them
- In Chrome, `fetch` can be used for static files and `script[type=module]` I
  think can also be used

### `localhost`

Serve the directory using a static file server, such as `serve`.

Pros:

- Can make CORS requests so long their `Access-Control-Allow-Origin` is set to
  `localhost`

Cons:

- `Access-Control-Allow-Origin` does not allow specifying multiple origins so
  the backend needs to switch between the local and remote origins dynamically
  based on the Host header
- In case the backend is behind a CDN and does not have access to the Host
  header of the initial peer, it cannot dynamically set the CORS ACAO header
- `Access-Control-Allow-Origin: *` can be used, but in that case, with fetch,
  `credentials: 'include'` will not be allowed - no cookies for you
- `localhost` is weird about cookies, the `Domain` bit must not be set fo the
  cookie to get set

### `localhost` with hosts

Pros:

- Very closely approximates the networking situation of the remote counterpart
- CORS ACAO can be set to a single value reusable with local and remote so long
  both are on the same protocol
- Allows for simulating all subdomaining scenarios as they relate to cookies

Cons:

- Still doesn't solve the problem with CORS ACAO single value if the remote uses
  HTTPS because the local has to, too, for which it needs a self-signed cert

### `localhost` with hosts and a self-signed certificate

Pros:

- Behaves the closest like the real thing

Cons:

- Need to make and maintaing own self-signed certificates and keep setting the
  browsers to trust them
