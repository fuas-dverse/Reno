Create at 23-02-2024 @ 09:18 by Reno Muijsenberg

# Context
To achieve the goal of having a large bot (agents) network, it is essential to have multiple agents that can work together to achieve a certain goal. In this file I will document the firsts steps taken to create such an agent, For this agent I want to create a simple chat bot that is made in Python, for this I will be using the ChatterBot package.

System requirements used:

| Type | Device                                  |
| ---- | --------------------------------------- |
| CPU  | Intel(R) Core(TM) i5-8400 CPU @ 2.80GHz |
| GPU  | NVIDIA GeForce GTX 1060 6GB             |
| RAM  | 16GB DDR4 @ 3600MHz                     |
# Using DeepMind Example

For this first try I will use the the steps explained on the GitHub page of Google DeepMind. (https://github.com/google-deepmind/gemma)

Install Jax: https://jax.readthedocs.io/en/latest/installation.html
```pip
pip install --upgrade pip
pip install --upgrade "jax[cpu]"
```

Install Gemma packages:
```pip
pip install git+https://github.com/google-deepmind/gemma.git
```

(Error, did not install above command, if I take a guess on why this error pops-up then it probably has to do something with my system running on Windows instead of Linux)

# Using HuggingFace Example
For the second try I will use the example provided by HuggingFace, on the HuggingFace website there are some examples on how to run this model, for example via CPU, GPU etc.
## Running the Gemma on CPU
Code:
```python
from transformers import AutoTokenizer, AutoModelForCausalLM

tokenizer = AutoTokenizer.from_pretrained("google/gemma-2b-it")
model = AutoModelForCausalLM.from_pretrained("google/gemma-2b-it")

input_text = "Write me a poem about Machine Learning."
input_ids = tokenizer(input_text, return_tensors="pt")

outputs = model.generate(**input_ids, max_new_tokens=4000)
print(tokenizer.decode(outputs[0]))
```

Output:
```console
<bos>Write me a poem about Machine Learning.

Machines, they weave and they learn,
From the data, they discern.
Algorithms, a symphony,
Unleash the power of the machine.

With each iteration, they grow,
A tapestry of insights they sow.
From the past, they learn and they adapt,
A never-ending, ever-fast.

The future holds mysteries untold,
Where machines will shape our world.
They will heal, they will fight, they will play,
A symphony of the digital day.

So let the machines learn and let them soar,
For the future is here, and it's here to stay.
With a touch of humanity, a spark of care,
We embrace the machine, and we share.<eos>
```

After having a successful run of Google's Gemma model using the CPU of my computer, I want to know the execution time it took to complete this answer.

For this I add some python code that adds a start date to the program, and when the program finishes I calculate the time between the start and now.

```python
from datetime import datetime  
start = datetime.now()


# At end of program
print(datetime.now() - start)
```

The output of this time calculation was:
```console
0:01:49.689571
```

## Running the Gemma on CPU & GPU
Code: 
Before running this code run:

```console
pip install accelerate
```

```python
from transformers import AutoTokenizer, AutoModelForCausalLM  
  
tokenizer = AutoTokenizer.from_pretrained("google/gemma-2b-it")  
model = AutoModelForCausalLM.from_pretrained("google/gemma-2b-it", device_map="auto")  
  
input_text = "Write me a poem about Machine Learning."  
input_ids = tokenizer(input_text, return_tensors="pt").to("cuda")  
  
outputs = model.generate(**input_ids, max_new_tokens=4000)  
print(tokenizer.decode(outputs[0]))
```

While running this project we can see in the terminal that it is actually using the GPU, but as my GPU is not strong enough it loads the model on the CPU and uses the GPU to process the question:
![[Pasted image 20240223102651.png]]

Output:
```console
<bos>Write me a poem about Machine Learning.

Machines, they weave and they learn,
From the data, they discern.
Algorithms, a symphony,
Unleash the power of the machine.

With each iteration, they grow,
A tapestry of insights they sow.
From the past, they learn and they adapt,
A never-ending, ever-fast.

The future holds mysteries untold,
Where machines will shape our world.
They will heal, they will fight, they will play,
A symphony of the digital day.

So let the machines learn and let them soar,
For the future is here, and it's here to stay.
With a touch of humanity, a spark of care,
We embrace the machine, and we share.<eos>
```

And after adding the run time again, and running it again the elapsed time is:
```console
0:03:02.319925
```
(Which is 2x as much as just using the CPU)
## Running the Gemma on GPU only
For this example we use the above steps to setup the code, the only line change is:
From:
```python
model = AutoModelForCausalLM.from_pretrained("google/gemma-2b-it", device_map="auto")  
```

To:
```python
model = AutoModelForCausalLM.from_pretrained("google/gemma-2b-it").to("cuda")
```

This will result in the model loading on the GPU and running on the GPU, still it has the same output as the above model but we notice that this approach is a slight bit faster as it only took:
```console
0:02:12.378486
```

### Running the model on a GPU using different precisions
#### - Using `torch.float16`
Change model code to:
```python
model = AutoModelForCausalLM.from_pretrained(  
    "google/gemma-2b-it",  
    torch_dtype=torch.float16,  
).to("cuda")
```
Output:
```console
<bos>Write me a poem about Machine Learning.

Machines, they weave and they learn,
From the data, they discern.
Algorithms, a symphony,
Unleash the power of the machine.

With each iteration, they grow,
A tapestry of insights they sow.
From the past, they learn and they adapt,
A never-ending, ever-fast.

The future holds mysteries untold,
Where machines will shape our world.
They will heal, they will fight, they will play,
A symphony of the digital day.

So let the machines learn and let them soar,
For the future is here, and it's here to stay.
With a touch of humanity, a spark of care,
We embrace the machine, and we share.<eos>
```

Results in response time of:
```console
0:00:39.548552
```

#### - Using `torch.bfloat16
Change model code to:
```python
model = AutoModelForCausalLM.from_pretrained(  
    "google/gemma-2b-it",  
    torch_dtype=torch.bfloat16,  
).to("cuda")
```
Output:
```console
<bos>Write me a poem about Machine Learning.

Machines, they weave and they learn,
From the data, they discern.
Algorithms, a symphony,
Unleash the power of the machine.

Data as the canvas, a masterpiece,
Algorithms paint, a new release.
From regression to classification,
Insights emerge, a revelation.

The future unfolds, ever so bright,
With machine learning, a guiding light.
From healthcare to finance,
It's shaping our world, in every line.

So let the machines learn, and let them soar,
Unlocking possibilities, forevermore.
For in the realm of the digital age,
Machine learning shines, with a brilliant page.<eos>
```
Results in response time of:
```console
0:00:40.000279
```

#### - Using `8-bit precision (int8)`
```console
pip install accelerate
pip install -i https://pypi.org/simple/ bitsandbytes
```

Change model code to:
```python
model = AutoModelForCausalLM.from_pretrained(  
    "google/gemma-2b-it",  
    quantization_config=BitsAndBytesConfig(load_in_8bit=True)  
).to("cuda")
```

Output:
**ERROR:** CUDA Setup failed despite GPU being available - Fix: ???

### Errors list
Error:
```console
Traceback (most recent call last):
  File "E:\Fontys\S6\ChatBot\GemmaChatBot - Python\main.py", line 8, in <module>
    input_ids = tokenizer(input_text, return_tensors="pt").to("cuda")
                ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
  File "E:\Fontys\S6\ChatBot\GemmaChatBot - Python\.venv\Lib\site-packages\transformers\tokenization_utils_base.py", line 789, in to
    self.data = {k: v.to(device=device) for k, v in self.data.items()}
                    ^^^^^^^^^^^^^^^^^^^
  File "E:\Fontys\S6\ChatBot\GemmaChatBot - Python\.venv\Lib\site-packages\torch\cuda\__init__.py", line 293, in _lazy_init
    raise AssertionError("Torch not compiled with CUDA enabled")
AssertionError: Torch not compiled with CUDA enabled
```

Actions:
- Install NVCC: https://developer.nvidia.com/cuda-downloads
- After install run, and get the following output
```console
C:\Windows\System32>nvcc --version
nvcc: NVIDIA (R) Cuda compiler driver
Copyright (c) 2005-2023 NVIDIA Corporation
Built on Wed_Nov_22_10:30:42_Pacific_Standard_Time_2023
Cuda compilation tools, release 12.3, V12.3.107
Build cuda_12.3.r12.3/compiler.33567101_0
```
- This means I have a cuda_12.3 build
- Go to: https://pytorch.org/get-started/locally/, and select your system
- Run the given pip install command, for me it was: 
```console
pip3 install torch torchvision torchaudio --index-url https://download.pytorch.org/whl/cu121
```
- When all these steps complete, go to python console and type:
```python
torch.cuda.is_available()
```
Which will return True or False.