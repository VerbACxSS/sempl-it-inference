# SEMPL-IT inference
This is the LLM inference application of the SEMPL-IT, a web app designed to simplify Italian administrative document using three different fine-tuned LLMs.

Check out the [frontend](https://github.com/VerbACxSS/semp-it-frontend) and the [backend](https://github.com/VerbACxSS/semp-it-backend) of the SEMPL-IT web app.

## Getting started
### Pre-requisites
This LLM inference application is developed using vllm framework and Hugging Face models. A modern NVIDIA GPU with 10GB+ of VRAM is required. The following software are required to run the application:
* Python (tested with version 3.12.8)
* Pip (tested with version 23.2.1)

Alternatively, you can use a containerized version by installing:
* Docker (tested on version 28.0.1)

### Configuration
The application can be configured using the following environment variable:
```
VLLM_API_KEY=12345678
```

### Using `python` and `pip`
Create python virtual environment
```sh
python3 -m venv venv
```

Activate the virtual environment
```sh
source venv/bin/activate
```

Install all dependencie
```sh
pip install vllm==8.0.1
pip install autoawq==0.2.8
```

Start the inference server
```sh
vllm serve --model=Qwen/Qwen2.5-7B-Instruct-AWQ \
--quantization=awq \
--load-format=auto \
--gpu-memory-utilization=0.95 \
--max-model-len=6144 \
--enable-lora \
--max-loras=8 \
--max_lora_rank=64 \
--lora-modules \
{\"name\":\"proofreading\",\"path\":\"VerbACxSS/sempl-it-proofreading-awq\"} \
{\"name\":\"lex\",\"path\":\"VerbACxSS/sempl-it-lex-awq\"} \
{\"name\":\"connectives\",\"path\":\"VerbACxSS/sempl-it-connectives-awq\"} \
{\"name\":\"expressions\",\"path\":\"VerbACxSS/sempl-it-expressions-awq\"} \
{\"name\":\"sentence_splitter\",\"path\":\"VerbACxSS/sempl-it-sentence-splitter-awq\"} \
{\"name\":\"nominalizations\",\"path\":\"VerbACxSS/sempl-it-nominalizations-awq\"} \
{\"name\":\"verbs\",\"path\":\"VerbACxSS/sempl-it-verbs-awq\"} \
{\"name\":\"sentence_reorganizer\",\"path\":\"VerbACxSS/sempl-it-sentence-reorganizer-awq\"}
```

### Using `docker`
Run the application using `docker`
```sh
docker run --gpus=all -p 8000:8000 --env-file .env --name sempl-it-models vllm/vllm-openai:v0.8.1 \
--model=Qwen/Qwen2.5-7B-Instruct-AWQ \
--quantization=awq \
--load-format=auto \
--gpu-memory-utilization=0.95 \
--max-model-len=6144 \
--enable-lora \
--max-loras=8 \
--max_lora_rank=64 \
--lora-modules \
{\"name\":\"proofreading\",\"path\":\"VerbACxSS/sempl-it-proofreading-awq\"} \
{\"name\":\"lex\",\"path\":\"VerbACxSS/sempl-it-lex-awq\"} \
{\"name\":\"connectives\",\"path\":\"VerbACxSS/sempl-it-connectives-awq\"} \
{\"name\":\"expressions\",\"path\":\"VerbACxSS/sempl-it-expressions-awq\"} \
{\"name\":\"sentence_splitter\",\"path\":\"VerbACxSS/sempl-it-sentence-splitter-awq\"} \
{\"name\":\"nominalizations\",\"path\":\"VerbACxSS/sempl-it-nominalizations-awq\"} \
{\"name\":\"verbs\",\"path\":\"VerbACxSS/sempl-it-verbs-awq\"} \
{\"name\":\"sentence_reorganizer\",\"path\":\"VerbACxSS/sempl-it-sentence-reorganizer-awq\"}
```

### Using `docker compose`
Run the application using `docker compose`
```sh
docker compose up --build -d
```

## Usage
The LLM inference application will be running at http://localhost:8000 by default.

Make a POST request to the following endpoint to simplify an administrative text:
```sh
curl -X POST "http://localhost:8000/v1/chat/completions" \
-H "Content-Type: application/json" \
-H "Authorization: Bearer VLLM_API_KEY" \
-d '{
    "model": "proofreading",
    "messages": [
        {
        "role": "system",
        "content": "Sei un esperto redattore di documenti istituzionali italiani.\n\nCorreggi gli errori ortografici, grammaticali, sintattici, di coesione, di punteggiatura e di preposizioni. **Non alterare il contenuto e lo stile del testo originale**.\n\n# Steps\n1. Leggi attentamente il testo istituzionale fornito.\n2. Identifica gli errori di ortografia, grammatica, sintassi, coesione, punteggiatura e preposizioni.\n3. Correggi gli errori individuati.\n\n# Output Format\nIl testo corretto con la stessa formattazione e suddivisione in paragrafi dell'originale.\n\n# Notes\n- Il testo fornito può essere complesso e richiede attenzione ai dettagli."
      },
      {
        "role": "user",
        "content": "Il documento individua le esigenze di sviluppo necessarie per assicurare che i principi delineati dalla Legge Regionale 23 dicembre 2004, n. 29 e dai successivi atti normativi, sulla essenziale funzione della ricerca e innovazione nelle Aziende Sanitarie della Regione Emilia-Romagna, si traducano in azioni concrete nel Servizio Sanitario Regionale.\n\nAlla luce delle evidenze della letteratura internazionale, delle indicazioni della normativa nazionale e della valutazione di quanto già attuato a livello regionale negli anni passati, vengono individuati gli obiettivi di sviluppo e le linee per il raggiungimento dei suddetti obiettivi."
      }
    ]
}'
```
## Built with
* [Huggingface](https://huggingface.co/)
* [vllm](https://github.com/vllm-project/vllm)

## License
This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## Acknowledgements
This contribution is a result of the research conducted within the framework of the PRIN 2020 (Progetti di Rilevante Interesse Nazionale) "VerbACxSS: on analytic verbs, complexity, synthetic verbs, and simplification. For accessibility" (Prot. 2020BJKB9M), funded by the Italian Ministero dell'Università e della Ricerca.