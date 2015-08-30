# Diffbot PHP client documentation

These are the source files for the official Diffbot PHP client documentation

See the built and hosted version at http://diffbot-php.readthedocs.org

See the client source [here](https://github.com/swader/diffbot-php-client).

## Contributing

If you'd like to contribute with a translation to your language, or edit the existing translations, please do the following:

### Prerequisites

- clone this repo.
- `pip` install the pre-requisites from the `requirements.txt` file.

### First use

- create the `po` files for your language with `bin/update.sh xx` where `xx` is your language code. Example:
    
    ```
    bin/update.sh jp # generates Japanese .po files under /locale/jp/LS_MESSAGES
    ```

### Adding / editing translations

- whenever the source files change (`.rst` files), run `bin/update.sh`.
- whenever you change the translations (`.po` files), run `bin/build.sh xx` where `xx` is your language code. This rebuilds the HTML for the given language, so you can inspect it in your browser - just open `index.html` in `_build/html/xx`.

### Troubleshooting

Open an issue if you run into problems.