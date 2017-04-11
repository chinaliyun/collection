datetime类型字段不要添加长度
```
CREATE TABLE `tender` (
  `id` int(10) UNSIGNED NOT NULL COMMENT 'id',
  `author_id` varchar(20) NOT NULL COMMENT '发布人ID',
  `title` varchar(50) NOT NULL COMMENT '标书名称',
  `begin` datetime NOT NULL COMMENT '开始时间',
  `end` datetime NOT NULL COMMENT '结束时间',
  `address` varchar(50) NOT NULL COMMENT '活动地址',
  `description` varchar(200) NOT NULL COMMENT '活动说明',
  `cover_img` varchar(100) NOT NULL COMMENT '封面图片地址',
  `time` datetime(6) NOT NULL COMMENT '发布时间',// 注意就是这里出错了
  `ip` varchar(10) NOT NULL COMMENT '发布IP'
) ENGINE=InnoDB DEFAULT CHARSET=utf8;
```