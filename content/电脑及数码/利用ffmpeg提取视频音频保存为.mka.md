---
title: 利用ffmpeg提取视频音频保存为.mka
date: 2025-09-28
lastmod: 2025-09-28
tags:
  - 电脑技术
categories: []
draft: false
---

---
## 🤖 Assistant

要使用FFmpeg从视频中提取音频并保存为`.mka`文件（Matroska音频容器），您可以执行以下命令：

### 基础命令：
```bash
ffmpeg -i input_video.mp4 -vn -c:a copy output_audio.mka
```

### 关键参数解释：
1. **`-i input_video.mp4`**
 * 指定输入视频文件（替换为您的视频路径）。

2. **`-vn`**（**V**ideo **N**o）
 * 禁用视频流处理，确保只提取音频。

3. **`-c:a copy`**
 * **核心参数**：将音频流原样复制（不重新编码），保留原始音质和编码格式（如AAC、FLAC、MP3等），处理速度极快。还可配合映射（`-map`）精确控制多个流：
 ```cmd
ffmpeg -i input.mkv -c:v libx264 -c:a:0 aac -c:a:1 mp3 output.mp4
# 视频编码为 H.264，第一个音频流编码为 AAC，第二个音频流编码为 MP3
   ```

4. **`output_audio.mka`**
 * 输出文件名，后缀`.mka`表示Matroska格式的纯音频容器。Matroska格式支持多种编码且兼容性好。

---

### 常见场景扩展：
#### 1. 提取指定音频流（例如多条音轨中的第2条）
```bash
ffmpeg -i input.mp4 -vn -map 0:a:1 -c:a copy track2.mka
```
* **`-map 0:a:1`**：
`0`=第一个输入文件，`a`=音频流，`1`=第二个音频流（索引从0开始）。

#### 2. 音频转码保存（如原始编码不支持.mka时）
```bash
ffmpeg -i input.mp4 -vn -c:a flac high_quality.flac.mka
```
* 将音频转码为FLAC格式（无损压缩），适合高品质需求，但处理速度较慢。

#### 3. 批量提取文件夹内所有视频的音频
```bash
for f in *.mp4; do ffmpeg -i "$f" -vn -c:a copy "${f%.mp4}.mka"; done
```
* 适用于Linux/Mac终端，将当前目录所有MP4视频的音频提取为同名.mka文件。

---

### 注意事项：
- **格式兼容性**：
`-c:a copy`要求原始音频编码必须兼容Matroska容器（常见如AAC、MP3、FLAC、Opus等均支持）。如果报错，改用转码参数（如`-c:a libmp3lame`提取为MP3）。

- **默认行为**：
若不指定`-map`参数，FFmpeg会自动选择第一条音频流（通常是主音轨）。

- **元数据保留**：
`.mka`会保留原始音频的元数据（如专辑、歌手信息）。

通过以上命令，您可以高效无损地从视频中分离音频到.mka文件，适合需要快速处理或保持原始音质的场景。
