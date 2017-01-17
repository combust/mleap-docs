# MLeap Documentation Development

We use [GitBook](https://www.gitbook.com/) to write our documentation.

## Install

Install the command line tools.

```
npm install -g gitbook-cli
```

Install `ebook-convert` to publish the PDF.

```
brew cask install calibre
```

## Local Development Server

Start a local server to see the documents.

```
gitbook serve
```

## Publish PDF

```
gitbook pdf
```
