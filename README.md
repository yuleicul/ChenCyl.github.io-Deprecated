# Blog

## 常用流程

```shell
hexo new "post title"

hexo serve

git push

hexo deploy
```

## 注意事项

- 删除一级标题
- excerpt 如果没有需要去掉
- 文章中的图片需要整理为一个文件夹，复制到 source/assets 下，文章中引用时使用 /assets/... 开头
- categories 目前有 Skills、阅读笔记、计算机基础、随笔。一般不设置二级分类，但需要的时候要注意它的语法：

```yaml
# 表示 Diary 分类下的 Life 子分类，而不是并列分类
categories:
  - Diary
  - Life

# 表示：
# Diary 分类下 PlayStation
# Diary 分类下 Games
# Life 分类
categories:
  - [Diary, PlayStation]
  - [Diary, Games]
  - [Life]
```


