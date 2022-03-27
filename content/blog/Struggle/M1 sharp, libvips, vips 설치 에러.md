---
title: 'M1 sharp, libvips, vips 설치 에러'
date: 2021-12-28
category: 'Struggle'
draft: false
---

## 에러 상황

`npm install`, `npm run dev` 등을 했을 때 아래와 같이 sharp module 설치에 문제가 생긴다는 문구가 뜬다.

```
ERR! sharp Prebuilt libvips 8.10.0 binaries are not yet available for darwin-arm64v8
```

구글링 해보면 https://github.com/lovell/sharp/issues/2460 이슈가 한창 진행됐었던게 보인다. 문제는 여기서 말해주는대로 해도 해결이 안 됐다. 혹시 도움이 될 수 있으니 내 맥북에서 해결된 방법을 써 본다.

## 해결방법

node v14.18.2 버전으로 다운그레이드

- 참고로 나는 homebrew 로 nvm을 설치했었는데 문제가 생길 수 있다고 해서 삭제하고 정식방법으로 nvm을 다시 설치했다.
- 그 후 `nvm install v14.18.2`
- `nvm use v14.18.2`

npm install -g npm@8.3.0  
brew uninstall vips  
brew install libvips

## 참고

https://dev.to/httpjunkie/setup-node-version-manager-nvm-on-mac-m1-7kl
https://stackoverflow.com/questions/67560211/mac-m1-something-went-wrong-installing-the-sharp-module/67566332
