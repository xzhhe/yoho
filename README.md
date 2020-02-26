[TOC]

## 1. yoho 是什么 ?

- 简单的理解为就是一个 CocoaPods 的二次封装的工具, 提供一些比较便捷的 CoaoaPods 操作
- 方便的修改 pod 的 **集成方式**
- 提供组件的 **二进制** 与 **源码** 的快速切换
- 提供丰富的 cli 代替 手动修改文件的操作, 很方便 CI 环境的 脚本代码使用
- 并且尽可能的 **可配置** 化
- 但是可能需要你对已有工程中的 Podfile 做一些修改



## 2. 在你的 App工程中, 如何接入 yoho ?

### Podfile 使用 yoho 接管 pod install

```ruby
target 'App' do
  Yoho::Installer.install!(self)
end
```

### 修改 yoho.json (必须)

```json

```

### 修改 yoho.custom.json (可选)

```json

```

### 使用 yoho DSL 拆解 Podfile 中的 pod('xx')

#### pod('xx')、pod('xx', '1.1.1')

```ruby
YohoFile.new do |y|
  y.name = 'AFNetworking'
  y.version = '3.0.0'
end
```

#### pod('xx', path: '...')

```ruby
YohoFile.new do |y|
  y.name = 'AFNetworking'
  y.path = '/path/to/AFNetworking'
end
```

#### pod('xx', git: 'xxx', tag/branch/commit: '...')

```ruby
YohoFile.new do |y|
  y.name = 'AFNetworking'
  y.git = 'xxx.git'
  y.branch = 'master'
end
```



## 3. yoho cli 快速修改 pod 的集成方式

### 修改为 local path

```shell
yoho custom --edit \
	--name 'AFNetworking' \
	--path '/path/to/AFNetworking'
```

### 修改为 git + tag

```shell
yoho custom --edit \
	--name 'AFNetworking' \
	--tag '1.1.1'
```

### 修改为 git + branch

```shell
yoho custom --edit \
	--name 'AFNetworking' \
	--branch 'dev'
```

### 修改为 git + commit

```shell
yoho custom --edit \
	--name 'AFNetworking' \
	--commit 'ad78ee'
```

### 修改为 all_source

```shell
yoho custom --edit \
	--name 'AFNetworking' \
	--all_source
```

### 修改为 all_binary

```shell
yoho custom --edit \
	--name 'AFNetworking' \
	--all_binary
```



## 4. pod  源码 与 二进制

- 修改 yoho.custom.json 提供 二进制文件 下载地址

  ```json
  {
    '':'',
    'bianry_download': 'http://xxx.api?name='
  }
  ```

- yoho 在集成的时候, 会自动 **优先使用二进制文件**

- 如果没有二进制文件, 则会保持使用源码

- 但是有一些组件 (动态库、静态库SDK) 本身就是 二进制 形态的, 不需要进行 源码 和 二进制 之间的切换

  ```ruby
  YohoFile.new do |y|
    y.name = 'AlibcSdk'
    y.git = 'xxx.git'
    y.binary = true # 告诉 yoho 这个组件本身就是 二进制 形态, 不需要进行切换操作
  end
  ```

  