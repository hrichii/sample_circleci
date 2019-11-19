# circleci使ってみた


# .circleci/config.yml
```yaml
version:

executors:
  default:
    working_directory: ~/workspace
    docker:
      - image: node:12

commands:
  restore_npm:
    steps:
      - restore_cache:
          name: Restore npm dependencies
          key: npm-{{ checksum "package.json" }}

jobs:
  build:
    docker:
      - image: circleci/golang:1.8
      - image: acme-private/private-image:321
        auth:
          username: mydockerhub-user
          password: $DOCKERHUB_PASSWORD
      - image: account-id.dkr.ecr.us-east-1.amazonaws.com/org/repo:0.1
        aws_auth:
          aws_access_key_id: AKIAQWERVA
          aws_secret_access_key: $ECR_AWS_SECRET_ACCESS_KEY
    environment:
      FOO: bar
    parallelism: 3
    resource_class: large
    working_directory: ~/my-app
  steps:
    - run: make test
    - run: make


workflows:
  pull-request:
    jobs:
      - setup
      - lint:
          requires:
            - setup
      - build:
          requires:
            - lint
      - test:
          requires:
            - build
```
## HP
https://hrichii.github.io/sample_circleci/

## 参考文献
- [CircleCIでHugoを実行してGitHub Pagesにデプロイ](https://t32k.me/mol/log/hugo-circleci-ghpages-2018/)
- [CircleCIでHugoのビルドを自動化した話](https://www.ted027.com/post/circleci/)
- [Qiita Circle CI で Github に write access 可能な Deploy key を設定する](https://qiita.com/boushi-bird@github/items/6b6eb1d1ed6f6d3341e4)
