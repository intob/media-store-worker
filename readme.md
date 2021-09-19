# media-store-worker
media-store-worker is a Cloudflare Worker that uses Cloudflare KV to store & serve media files very quickly.

Your application must authenticate to PUT new media files, this is currently implemented using GitHub's auth0 API. When you PUT a new media file, you get a permalink back:
```
https://{workersDomain}/{mimeType}/{contentHash}`
```


## Deployment
Deploy this Worker using the [wrangler](https://developers.cloudflare.com/workers/cli-wrangler) CLI

You must set the following secrets either using wrangler CLI or in your Cloudflare Workers dashboard:
- GITHUB_CLIENT_ID `wrangler secret put GITHUB_CLIENT_ID`
- GITHUB_CLIENT_SECRET `wrangler secret put GITHUB_CLIENT_SECRET`

You must also enter your Cloudflare account details in `wrangler.toml`, so wrangler can deploy your worker.

Finally, deploy the Worker...
```bash
wrangler publish
```

## Usage
PUT an image, for example...
```
curl --location --request PUT 'https://media-store.dr-useless.workers.dev/' \
--header 'Authorization: Bearer [MY_GITHUB_APP_TOKEN]' \
--header 'Content-Type: image/jpeg' \
--data-binary '@~/Pictures/wow.jpg'
```
Responds with:
```
https://media-store.dr-useless.workers.dev/image/jpeg/30ab85b49559810996e6ad256ee51708e0180f991341c8e7430824a04767474e
```
- This URL contains a hash of the content, and will never change.
- On each request, this key will refresh if inside the `refreshThreshold`.
- This key will expire `keyTtl` seconds after the last refresh.

This media-store will not bloat with old keys, as they expire if they are not requested for the configured `keyTtl`.


## Contributing
Pull requests are welcome. For major changes, please open an issue first to discuss what you would like to change.

Definitely needed:
- `config.js`
- Provide an agnostic/generic interface for handling authentication


## License
[MIT](https://choosealicense.com/licenses/mit/)