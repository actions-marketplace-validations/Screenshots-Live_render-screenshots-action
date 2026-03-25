# Screenshots.live Render Action

Generate app store screenshots automatically in your GitHub Actions workflow via the [Screenshots.live](https://screenshots.live) REST API.

## Usage

```yaml
name: Generate Screenshots
on:
  release:
    types: [published]

jobs:
  screenshots:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Render screenshots
        uses: screenshots-live/render-screenshots-action@v1
        with:
          api-key: ${{ secrets.SCREENSHOTSLIVE_API_KEY }}
          yaml-config: ./screenshots.yml

      - name: Upload as artifact
        uses: actions/upload-artifact@v4
        with:
          name: app-store-screenshots
          path: ./screenshots/
```

## Inputs

| Input | Required | Default | Description |
|-------|----------|---------|-------------|
| `api-key` | Yes | - | Your Screenshots.live API key |
| `yaml-config` | Yes | - | Path to YAML render configuration |
| `output-directory` | No | `./screenshots` | Where to save rendered files |
| `api-url` | No | `https://api.screenshots.live` | Override API URL |
| `poll-interval` | No | `3` | Seconds between status checks |
| `timeout` | No | `600` | Max seconds to wait |

## Outputs

| Output | Description |
|--------|-------------|
| `output-path` | Path to rendered screenshots directory |
| `job-id` | The render job ID |

## Output Structure

```
screenshots/
  en-US/
    iPhone 6.5/
      screenshot_01.png
      screenshot_02.png
    iPad 12.9/
      screenshot_01.png
    phoneScreenshots/
      screenshot_01.png
    tenInchScreenshots/
      screenshot_01.png
```

## Full CI/CD Example

```yaml
name: Release with Screenshots
on:
  release:
    types: [published]

jobs:
  screenshots:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Generate screenshots
        uses: screenshots-live/render-screenshots-action@v1
        id: render
        with:
          api-key: ${{ secrets.SCREENSHOTSLIVE_API_KEY }}
          yaml-config: ./screenshots.yml
          output-directory: ./fastlane/screenshots

      - name: Upload to App Store Connect
        run: fastlane deliver --skip_metadata --skip_binary_upload
        env:
          APP_STORE_CONNECT_API_KEY: ${{ secrets.ASC_API_KEY }}
```

## License

MIT
