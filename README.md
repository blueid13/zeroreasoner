
## 🎄 Environment Setup
```bash
(클론한 디렉토리 가셔서 가상환경 만드시고, cuda 툴킷 설치 필요합니다)
cd verl
pip install -e .
cd ..

python -m pip install "https://github.com/adithyaxx/flash-attention/releases/download/v2.8.3/flash_attn-2.8.3+cu13torch2.11cxx11abiTRUE-cp312-cp312-linux_x86_64.whl"

pip install -r requirements.txt
python -m pip install -U vllm --extra-index-url https://download.pytorch.org/whl/cu129
pip install "math-verify[antlr4_9_3]"
pip install debugpy
pip install -U "transformers[serving] @ git+https://github.com/huggingface/transformers.git@main"
python -m pip uninstall -y tensordict
python -m pip install tensordict==0.6.2
python -m pip check
```

## 💾 Data Processing
### Process evaluation data on CruxEval / LiveCodeBench Execution during AZR Self-play
```bash
python -m absolute_zero_reasoner.data_construction.process_code_reasoning_data
```


## ♟️ Self-play
```bash
bash scripts/selfplay/2b.sh
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
