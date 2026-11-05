# Course Hub — 校园课程评价与学习资料平台

Node.js + Express 全栈项目，数据持久化在 `server/data.json`（JSON 文件，无外部数据库）。

## 运行

```bash
npm install
npm start
# 服务运行在 http://localhost:3032
```

首页：`/`（课程 / 评价 / 资料 / 问答 / 学习小组 / 教师 / 收藏，多视图前端）
管理后台：`/admin.html`（数据统计、评价审核通过/驳回、删除评价与课程）

## 数据（每类 12 条种子数据）

- courses（课程）：课程代码、名称、教师、学院、学分、学期、评分均值
- reviews（评价）：评分 1–5、难度、负担、评语、点赞、审核状态
- materials（资料）：标题、所属课程、类型（课件/笔记/真题/习题/其他）、上传者、下载量、大小
- questions（问答）：标题、内容、所属课程、状态（open/answered/closed）
- answers（回答）：挂在问题下，可采纳为最佳答案
- studyGroups（学习小组）：课程、时间、地点、人数上限、成员
- teachers（教师）：姓名、学院、职称、邮箱、评分
- collections（收藏）：用户收藏的课程或资料

## API（41 个端点）

- `GET /api/health`
- `GET /api/search?q=关键词`（课程 + 资料 + 教师 + 问题）
- 课程：`GET /api/courses`（department/semester/teacher/minRating 过滤，q 搜索，rating_desc/credits_desc/newest 排序，page/size 分页）、`GET /api/courses/:id`（含该课评价、资料、问题）、`POST /api/courses`、`PUT /api/courses/:id`、`DELETE /api/courses/:id`
- 评价：`GET /api/reviews`（courseId/status/minRating/q 过滤，likes_desc/rating_desc 排序）、`GET /api/reviews/:id`、`POST /api/reviews`、`PUT /api/reviews/:id`、`DELETE /api/reviews/:id`、`POST /api/reviews/:id/like`
- 资料：`GET /api/materials`（courseId/type/q 过滤，downloads_desc 排序）、`GET /api/materials/:id`、`POST /api/materials`、`PUT /api/materials/:id`、`DELETE /api/materials/:id`、`POST /api/materials/:id/download`
- 问答：`GET /api/questions`、`GET /api/questions/:id`（含回答）、`POST /api/questions`、`PUT /api/questions/:id`、`DELETE /api/questions/:id`、`POST /api/questions/:id/answers`、`PUT /api/answers/:id`、`POST /api/answers/:id/accept`
- 学习小组：`GET /api/study-groups`、`GET /api/study-groups/:id`、`POST /api/study-groups`、`PUT /api/study-groups/:id`、`DELETE /api/study-groups/:id`、`POST /api/study-groups/:id/join`、`POST /api/study-groups/:id/leave`
- 教师：`GET /api/teachers`（department/q 过滤，rating_desc 排序）、`GET /api/teachers/:id`（含所授课程）、`POST /api/teachers`、`PUT /api/teachers/:id`、`DELETE /api/teachers/:id`
- 收藏：`GET /api/collections`（user/kind 过滤）、`POST /api/collections`、`DELETE /api/collections/:id`

## 校验规则

- 评价 rating/difficulty/workload 必须是 1–5 的整数，否则 400；课程不存在时 404
- 课程代码重复 → 409；学分须为 0–10 的数字，否则 400
- 资料 type 必须是 课件/笔记/真题/习题/其他，否则 400
- 评价 status 只能是 pending/approved/rejected；问题 status 只能是 open/answered/closed
- 学习小组重复加入 → 409；小组满员 → 409；容量小于现有人数时改容量 → 409；退出非成员 → 404
- 收藏重复 → 409；收藏 kind 只能是 course/material
- PUT 只允许白名单字段，id/createdAt 不可修改
- 不存在的资源 → 404
