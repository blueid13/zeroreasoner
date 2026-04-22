
## 🎄 Environment Setup
```bash
(대충 클론한 디렉토리 가셔서)
conda create -n {가상환경이름} python=3.10
conda activate {가상환경이름}
conda install nvidia/label/cuda-12.4.1::cuda-toolkit
cd verl
pip install -e .
cd ..
pip install wheel

(nvcc 관련해서 경로 문제 생길수있음 조심하십쇼)
pip install flash-attn --no-build-isolation
pip install -r requirements.txt
pip uninstall vllm
pip install vllm==0.7.3
pip install transformers==4.47.1
pip install "math-verify[antlr4_9_3]"
pip install debugpy
```

## 💾 Data Processing
### Process evaluation data on CruxEval / LiveCodeBench Execution during AZR Self-play
```bash
python -m absolute_zero_reasoner.data_construction.process_code_reasoning_data
```

<!-- ============================================== -->
<div align="left">
  <h1 id="training">🏋️ Training</h1>
  <hr style="height: 3px; background: linear-gradient(90deg, #EF8E8D, #5755A3); border: none; border-radius: 3px;">
</div>

> **⚠️WARNING⚠️**: The Python executor in this repository is very raw and intended for research purposes only. It is not secure for production environments. We plan to update our executor to more secure implementations in the future. Your use of our code is at your own discretion and risk.


## 🫛 Seeding (Optional)
We provide the seed datasets we collected by prompting each model in data/. If you want to create your own seed data, use the following script:
```bash
export OUTPUT_SEED_PATH=data/<new_ded_abd_seed_data_name>.jsonl
export OUTPUT_CODE_F_SEED_PATH=data/<new_ind_seed_data_name>.jsonl
bash scripts/seeding/<7b|14b|coder3b|coder7b|coder14b|llama>.sh
```

## ♟️ Self-play
3b models need 2 X 80gb GPUs, 7/8b models need 4 X 80gb, 14b requires 8 X 80gb
```bash
bash scripts/selfplay/<7b|14b|coder3b|coder7b|coder14b|llama>.sh
```
If you want to use your own ded/abd or ind seed dataset:
```bash
export OUTPUT_SEED_PATH=data/<your_ded_abd_seed_data_name>.jsonl
export OUTPUT_CODE_F_SEED_PATH=data/<your_ind_seed_data_name>.jsonl
bash scripts/selfplay/<7b|14b|coder3b|coder7b|coder14b|llama>.sh
```

## 🌚 Resuming Runs
When resuming runs, put the original run wandb id into the script, i.e., `trainer.wandb_run_id=<run_id>`.

## 🤗 Converting veRL checkpoints to HF format
```bash
python -m absolute_zero_reasoner.utils.convert2hf \
  <veRL_ckpt_path>/actor \
  <veRL_ckpt_path>/actor/huggingface/ \
  <hf_ckpt_path>
```

## 📈Design Your Own Intrinsic Rewards!
In configs, just add your own rewards to `azr.reward.generation_reward_config`, check the ones already implemented such as diversity and complexity rewards. Be Creative!

<!-- ============================================== -->
<div align="left">
  <h1 id="usage">🔧 Usage</h1>
  <hr style="height: 3px; background: linear-gradient(90deg, #EF8E8D, #5755A3); border: none; border-radius: 3px;">
</div>

We use the Deepseek R1 <think> & <answer> tags as prompt template:

```
A conversation between User and Assistant. The user asks a question, and the Assistant solves it. The assistant first thinks about the reasoning process in the mind and then provides the user with the answer. The reasoning process and answer are enclosed within <think> </think> and <answer> </answer> tags, respectively, i.e., <think> reasoning process here </think> <answer> answer here </answer>. User: {question}\nAssistant: <think>
```

<!-- ============================================== -->
<div align="left">
  <h1 id="evaluation-code">📃 Evaluation Code</h1>
  <hr style="height: 3px; background: linear-gradient(90deg, #EF8E8D, #5755A3); border: none; border-radius: 3px;">
</div>

## LiveCodeBench
Setup: LCB needs to first download the data
```bash
git clone https://hf-mirror.com/datasets/livecodebench/code_generation_lite evaluation/code_eval/coding/LiveCodeBench/code_generation_lite
```
Evaluation:
```bash
bash evaluation/code_eval/scripts/run_lcb_gen.sh --model <andrewzh/Absolute_Zero_Reasoner-Coder-3b>
```

## Evalplus
New conda env is neede for evalplus
```bash
conda create -n evalplus python=3.11
pip install --upgrade "evalplus[vllm] @ git+https://github.com/evalplus/evalplus@d362e933265c3e7e3df8101c930a89c3c470cd9f"
Evaluation:
```bash
condda activate evalplus
bash evaluation/code_eval/scripts/run_evalplus.sh 0 <humaneval|mbpp> <andrewzh/Absolute_Zero_Reasoner-Coder-3b>
```
