# gatsby-node.js

ビルドプロセスの処理が書かれる場所
`$ gatsby build` コマンドを叩いたときに、走る処理

onPostBuildとcreatePagesの2つのAPIを実装するgatsby-node.jsファイルの例。

```js
const path = require(`path`)
// Log out information after a build is done
exports.onPostBuild = ({ reporter }) => {
  reporter.info(`Your Gatsby site has been built!`)
}
// Create blog pages dynamically
exports.createPages = async ({ graphql, actions }) => {
  const { createPage } = actions
  const blogPostTemplate = path.resolve(`src/templates/blog-post.js`)
  const result = await graphql(`
    query {
      allSamplePages {
        edges {
          node {
            slug
            title
          }
        }
      }
    }
  `)
  result.data.allSamplePages.edges.forEach(edge => {
    createPage({
      path: `${edge.node.slug}`,
      component: blogPostTemplate,
      context: {
        title: edge.node.title,
      },
    })
  })
}
```


## APIs

### `createPages`

ページを動的に作成する。

リモートまたはローカルソースからデータをフェッチしてページを作成することも可能


Parameters

- actions
  -  createPage
- graphql : データクエリのためのapi
- reporter : ユーザーに情報を出力するためのユーティリティのセット 例） info, warn 等

```js
const path = require(`path`)

exports.createPages = ({ graphql, actions }) => {
  const { createPage } = actions
  const blogPostTemplate = path.resolve(`src/templates/blog-post.js`)
  // ページの作成に使用するマークダウンノードをクエリ
  // ページを作成するデータ等
  // 2番目の関数パラメーターとして変数を追加可能
  return graphql(`
    query loadPagesQuery ($limit: Int!) {
      allMarkdownRemark(limit: $limit) {
        edges {
          node {
            frontmatter {
              slug
            }
          }
        }
      }
    }
  `, { limit: 1000 }).then(result => {
    if (result.errors) {
      throw result.errors
    }

    // ブログ投稿ページ作成
    result.data.allMarkdownRemark.edges.forEach(edge => {
      createPage({
        // ページパス（必須）
        path: `${edge.node.frontmatter.slug}`,
        component: blogPostTemplate,
        context: {
          // 挿入するオプションのコンテキストデータをpropsとして追加
        },
      })
    })
  })
}
```

### `createPagesStatefully`

### `createResolvers`

```js
exports.createResolvers = ({ createResolvers }) => {
  const resolvers = {
    Author: {
      fullName: {
        resolve: (source, args, context, info) => {
          return source.firstName + source.lastName
        }
      },
    },
    Query: {
      allRecentPosts: {
        type: [`BlogPost`],
        resolve: async (source, args, context, info) => {
          const { entries } = await context.nodeModel.findAll({ type: `BlogPost` })
          return entries.filter(
            post => post.publishedAt > Date.UTC(2018, 0, 1)
          )
        }
      }
    }
  }
  createResolvers(resolvers)
}
```
