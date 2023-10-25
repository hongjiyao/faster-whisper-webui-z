

这是一个[ycyy/faster-whisper-webui](https://github.com/ycyy/faster-whisper-webui)的项目，我根据这个进行了修改以供个人使用。

# 在本地运行

要在本地运行此程序，首先安装Python 3.9+和Git。然后安装Pytorch 10.1+和其他所有依赖项：:
```
pip install -r requirements.txt
```
项目模型在本地加载，需要在项目路径中创建一个models目录，并将模型文件以以下格式放置。
```
├─faster-whisper
│  ├─base
│  ├─large
│  ├─large-v2
│  ├─medium
│  ├─small
│  └─tiny
└─silero-vad
    ├─examples
    │  ├─cpp
    │  ├─microphone_and_webRTC_integration
    │  └─pyaudio-streaming
    ├─files
    └─__pycache__
```
### 模型下载路径

[faster-whisper](https://huggingface.co/guillaumekln)

[silero-vad](https://github.com/snakers4/silero-vad)

使用并行CPU/GPU启用运行完整版本（无音频长度限制）的应用程序：
```
python app.py --input_audio_max_duration -1 --server_name 127.0.0.1 --auto_parallel True
```
    
#--input_audio_max_duration：音频文件的最大长度（以秒为单位），或者设置为-1表示无限制。
#--share：是否在HuggingFace上分享应用程序。
#--server_name：要绑定到的主机或IP。如果为None，则绑定到本地主机。
#--server_port：要绑定到的端口。
#--queue_concurrency_count：要处理的并发请求的数量。
#--default_model_name：默认模型名称。
#--default_vad：默认的语音活动检测（VAD）算法。
#--vad_initial_prompt_mode：是否在每个VAD段前添加初始提示（prepend_all_segments表示在所有段前添加，prepend_first_segment表示仅在前一段前添加）。
#--vad_parallel_devices：用于并行处理的CUDA设备列表。如果为None，则禁用并行处理。
#--vad_cpu_cores：用于VAD预处理的CPU核心数。
#--vad_process_timeout：进程终止前的秒数。设置为0表示立即关闭进程，设置为None表示无超时。
#--auto_parallel：是否使用所有可用的GPU和CPU核心进行处理。使用vad_cpu_cores/vad_parallel_devices指定要使用的CPU核心数/GPU数。
#--output_dir：保存输出的目录。
#--whisper_implementation：要使用的Whisper实现（"whisper"或"faster-whisper"）。
#--compute_type：用于推理的计算类型（"default"、"auto"、"int8"、"int8_float16"、"int16"、"float16"、"float32"）。
你还可以使用配置文件config.json5。有关更多信息，请参阅该文件。
如果你想使用不同的配置文件，你可以使用WHISPER_WEBUI_CONFIG环境变量指定另一个文件的路径

## Google Colab
如果你没有足够强大的GPU来运行大型模型，你还可以直接在[Google Colab](https://colab.research.google.com/drive/1qeTSvi7Bt_5RMm88ipW4fkcsMOKlDDss?usp=sharing)上运行这个Web UI, 
查看 [colab documentation](docs/colab.md) 以获取更多信息。

## Parallel Execution
你还可以同时在多个GPU上并行运行Web-UI或CLI，使用vad_parallel_devices选项。这需要一个逗号分隔的设备ID列表（0、1等），Whisper应该分布在这些设备上并同时运行：:
```
python cli.py --model large --vad silero-vad --language Japanese \
--vad_parallel_devices 0,1 "https://www.youtube.com/watch?v=4cICErqqRSM"
``` 
注意，这需要一个VAD才能正常工作，否则只会使用第一个GPU。虽然你可以使用period-vad来避免运行Silero-Vad的开销，但会稍微影响准确性。

这是通过创建N个子进程（其中N是所选设备的数量）来实现的，Whisper在这些子进程中并发运行。
在app.py中，你还可以设置vad_process_timeout选项。这个选项配置了一个进程因不活动而被杀死之前的秒数，以释放RAM和视频内存。默认值是30分钟.

```
python app.py --input_audio_max_duration -1 --vad_parallel_devices 0,1 --vad_process_timeout 3600
```

要并行执行Silero VAD本身，请使用vad_cpu_cores选项:
```
python app.py --input_audio_max_duration -1 --vad_parallel_devices 0,1 --vad_process_timeout 3600 --vad_cpu_cores 4
```

如果你更喜欢在一段时间后始终释放视频内存，你也可以在单个设备（--vad_parallel_devices 0）上使用vad_process_timeout。

###自动并行

你也可以将auto_parallel设置为True。这将设置vad_parallel_devices以使用系统上的所有GPU设备，并将vad_cpu_cores设置为等于核心数（最多为8）：
```
python app.py --input_audio_max_duration -1 --auto_parallel True
```

## One Key Start
对于新手用户，可以从页面下载免安装程序。点击window.bat 启动程序，然后在浏览器中输入相应的地址进行访问(仅包含tiny，其他模型需要单独下载) 。


###参考与学习
# GPT 学术优化 (GPT Academic):
[https://github.com/THUDM/ChatGLM2-6B](https://github.com/binary-husky/gpt_academic/tree/master)

# faster-whisper-webui:
[https://github.com/Jittor/JittorLLMs](https://github.com/ycyy/faster-whisper-webui)

