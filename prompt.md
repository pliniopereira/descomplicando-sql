# Prompt para DeepSeek: Analisador de Arquivos com Ollama

## 🎯 Objetivo
Crie um sistema Python robusto e profissional que:
1. Analise arquivos PowerPoint (.ppt/.pptx) e Excel (.xlsx/.xls) de uma pasta específica "analisar/"
2. Envie o conteúdo extraído para Ollama via biblioteca Python
3. Execute agentes Python baseados na resposta do Ollama
4. **Salve resultados em arquivos na pasta "processados/" com timestamp e nome original**

## 📋 Requisitos Técnicos

### Funcionalidades Principais:
- **Escaneamento automático** da pasta "analisar/"
- **Detecção automática** de tipos de arquivo (PPT/Excel)
- **Extração inteligente** de conteúdo (texto, tabelas, dados)
- **Integração robusta** com Ollama via biblioteca Python
- **Execução segura** de código Python gerado
- **Salvamento automático** na pasta "processados/" com formato: `YYYYMMDD_HHMMSS_nome_original.json`
- **Interface CLI** interativa e amigável

### Estrutura de Pastas:
```
projeto/
├── analyzer.py
├── analisar/           # Pasta de entrada (arquivos para processar)
│   ├── relatorio.xlsx
│   └── apresentacao.pptx
└── processados/        # Pasta de saída (resultados com timestamp)
    ├── 20240904_143052_relatorio.json
    ├── 20240904_143125_apresentacao.json
    └── ...
```

### Bibliotecas Necessárias:
```python
# Ollama - Biblioteca Python oficial
import ollama

# Processamento de arquivos
import pandas as pd
from pptx import Presentation  # python-pptx
import openpyxl

# Sistema e utilitários
import os, sys, logging
import json
from pathlib import Path
from typing import Dict, List, Optional, Any
from dataclasses import dataclass
from datetime import datetime
import asyncio  # Para operações assíncronas
```

### Instalação:
```bash
pip install ollama pandas python-pptx openpyxl
```

## 🏗️ Arquitetura Solicitada

### 1. Classes Principais:
```python
@dataclass
class FileInfo:
    """Estrutura para informações do arquivo"""
    path: str
    name: str
    type: str  # "PowerPoint" ou "Excel"
    size: int
    extension: str

class FileAnalyzer:
    """Analisador principal de arquivos"""
    def __init__(self):
        self.ollama_client = OllamaClient()
        self.setup_logging()
        self.setup_folders()
    
    def setup_logging(self):
        """Configura logging profissional"""
        pass
    
    def setup_folders(self):
        """Cria pastas necessárias se não existirem"""
        os.makedirs("analisar", exist_ok=True)
        os.makedirs("processados", exist_ok=True)
    
    def scan_folder(self, folder_path: str = "analisar") -> List[FileInfo]:
        """Escaneia pasta e retorna arquivos suportados"""
        pass
    
    def extract_content(self, file_info: FileInfo) -> str:
        """Extrai conteúdo baseado no tipo do arquivo"""
        pass
    
    def send_to_ollama(self, content: str, model: str = "llama2") -> Dict:
        """Envia para Ollama usando biblioteca Python"""
        pass
    
    def execute_agent(self, code: str, context: Dict = None) -> Dict:
        """Executa código de forma segura"""
        pass
    
    def save_results(self, file_info: FileInfo, analysis_result: Dict) -> str:
        """Salva resultados na pasta processados com timestamp"""
        # Formato: YYYYMMDD_HHMMSS_nome_original.json
        timestamp = datetime.now().strftime("%Y%m%d_%H%M%S")
        base_name = Path(file_info.name).stem
        output_filename = f"{timestamp}_{base_name}.json"
        output_path = os.path.join("processados", output_filename)
        
        # Estrutura completa do resultado
        complete_result = {
            "timestamp": timestamp,
            "processed_at": datetime.now().isoformat(),
            "source_file": {
                "name": file_info.name,
                "path": file_info.path,
                "type": file_info.type,
                "size": file_info.size
            },
            "analysis": analysis_result,
            "metadata": {
                "analyzer_version": "1.0.0",
                "model_used": "llama2"
            }
        }
        
        with open(output_path, 'w', encoding='utf-8') as f:
            json.dump(complete_result, f, indent=2, ensure_ascii=False)
        
        return output_path
    
    def process_file(self, file_info: FileInfo, model: str = "llama2") -> Dict:
        """Pipeline completo: extrair -> analisar -> executar -> salvar"""
        pass

class OllamaClient:
    """Cliente para interação com Ollama usando biblioteca Python"""
    def __init__(self):
        pass
    
    def generate(self, prompt: str, model: str = "llama2", 
                options: Dict = None) -> Dict:
        """Gera resposta usando ollama.generate()"""
        try:
            response = ollama.generate(
                model=model,
                prompt=prompt,
                options=options or {"temperature": 0.7}
            )
            return {
                "success": True,
                "response": response["response"],
                "model": model,
                "context": response.get("context", [])
            }
        except Exception as e:
            return {"success": False, "error": str(e)}
    
    def chat(self, messages: List[Dict], model: str = "llama2") -> Dict:
        """Chat usando ollama.chat() para conversas contextuais"""
        try:
            response = ollama.chat(
                model=model,
                messages=messages
            )
            return {
                "success": True,
                "response": response["message"]["content"],
                "model": model
            }
        except Exception as e:
            return {"success": False, "error": str(e)}
    
    def list_models(self) -> List[str]:
        """Lista modelos disponíveis usando ollama.list()"""
        try:
            models = ollama.list()
            return [model["name"] for model in models["models"]]
        except Exception as e:
            logging.error(f"Erro ao listar modelos: {e}")
            return []
    
    def pull_model(self, model: str) -> bool:
        """Baixa modelo se não existir usando ollama.pull()"""
        try:
            ollama.pull(model)
            return True
        except Exception as e:
            logging.error(f"Erro ao baixar modelo {model}: {e}")
            return False
```

### 2. Funcionalidades Específicas:

#### PowerPoint (.ppt/.pptx):
- Extrair texto de todos os slides
- Capturar conteúdo de tabelas e gráficos
- Identificar títulos e subtítulos
- Extrair notas do apresentador (se houver)
- Manter estrutura hierárquica

#### Excel (.xlsx/.xls):
- Processar todas as abas
- Extrair dados estruturados
- Calcular estatísticas básicas
- Identificar tipos de dados
- Detectar fórmulas importantes
- Preservar formatação relevante

### 3. Sistema de Salvamento:

#### Formato dos Arquivos de Saída:
```json
{
  "timestamp": "20240904_143052",
  "processed_at": "2024-09-04T14:30:52.123456",
  "source_file": {
    "name": "relatorio.xlsx",
    "path": "analisar/relatorio.xlsx",
    "type": "Excel",
    "size": 1024000
  },
  "analysis": {
    "resumo": "string",
    "insights": ["insight1", "insight2"],
    "recomendacoes": ["rec1", "rec2"],
    "codigo_agente": "print('Hello')",
    "execution_result": {
      "success": true,
      "output": "Hello"
    }
  },
  "metadata": {
    "analyzer_version": "1.0.0",
    "model_used": "llama2",
    "processing_time_seconds": 45.2
  }
}
```

#### Nomenclatura dos Arquivos:
- **Formato**: `YYYYMMDD_HHMMSS_nome_original.json`
- **Exemplos**:
  - `20240904_143052_relatorio.json`
  - `20240904_143125_apresentacao_vendas.json`
  - `20240904_150000_dados_financeiros.json`

### 4. Integração com Ollama (Biblioteca Python):

#### Métodos Principais da Biblioteca:
```python
import ollama

# 1. Geração de texto simples
response = ollama.generate(
    model='llama2',
    prompt='Analise este conteúdo...',
    options={
        'temperature': 0.7,
        'top_p': 0.9,
        'max_tokens': 2000
    }
)

# 2. Chat contextual (recomendado para análises)
messages = [
    {
        'role': 'system', 
        'content': 'Você é um especialista em análise de dados...'
    },
    {
        'role': 'user', 
        'content': 'Analise este conteúdo e retorne código Python...'
    }
]
response = ollama.chat(model='llama2', messages=messages)

# 3. Listar modelos disponíveis
models = ollama.list()
available_models = [model['name'] for model in models['models']]

# 4. Baixar modelo automaticamente
ollama.pull('llama2')
```

## 🎯 Prompt Template para Ollama:
```
Você é um analista de dados especializado. Analise o conteúdo fornecido e retorne:

1. **RESUMO EXECUTIVO** (2-3 frases principais)
2. **INSIGHTS CHAVE** (máximo 5 pontos)
3. **RECOMENDAÇÕES** (ações práticas)
4. **CÓDIGO AGENTE** (Python funcional para automação)

**REGRAS IMPORTANTES:**
- Use formato JSON estruturado
- O código deve ser executável e seguro
- Foque em insights acionáveis
- Seja conciso mas preciso

**CONTEÚDO PARA ANÁLISE:**
{content}

**RESPOSTA ESPERADA (JSON):**
{
  "resumo": "string",
  "insights": ["string1", "string2", ...],
  "recomendacoes": ["string1", "string2", ...],
  "codigo_agente": "string com código Python"
}
```

## ⚡ Boas Práticas Obrigatórias:

### 1. **Logging Profissional**:
```python
import logging
from datetime import datetime

# Configuração de logging com rotação
logging.basicConfig(
    level=logging.INFO,
    format='%(asctime)s - %(name)s - %(levelname)s - %(message)s',
    handlers=[
        logging.FileHandler(f'analyzer_{datetime.now():%Y%m%d}.log'),
        logging.StreamHandler()
    ]
)
```

### 2. **Controle de Arquivos Processados**:
- Verificar se arquivo já foi processado (opcional)
- Log de todos os arquivos processados
- Backup automático da pasta processados

### 3. **Tratamento de Erros**:
- Salvar erros também em arquivos JSON
- Continue processando outros arquivos em caso de erro
- Log detalhado de falhas

### 4. **Interface CLI Melhorada**:
```
🔍 Escaneando pasta 'analisar'...
📋 Arquivos encontrados:
   1. relatorio.xlsx (Excel) - 1.2 MB
   2. apresentacao.pptx (PowerPoint) - 850 KB

🚀 Processando arquivos...
✅ relatorio.xlsx → processados/20240904_143052_relatorio.json
✅ apresentacao.pptx → processados/20240904_143125_apresentacao.json

📊 Resumo:
   - 2 arquivos processados
   - Pasta de saída: processados/
   - Tempo total: 2m 15s
```

## 🚀 Exemplo de Implementação do Main:
```python
def main():
    analyzer = FileAnalyzer()
    
    # Verificar modelos Ollama
    models = ollama.list()
    if not any('llama2' in model['name'] for model in models['models']):
        print("📥 Baixando modelo llama2...")
        ollama.pull('llama2')
    
    # Processar arquivos
    files = analyzer.scan_folder("analisar")
    
    if not files:
        print("❌ Nenhum arquivo encontrado na pasta 'analisar/'")
        return
    
    print(f"🚀 Processando {len(files)} arquivo(s)...")
    
    results = []
    for file_info in files:
        print(f"📄 Processando: {file_info.name}")
        
        # Processar arquivo completo
        result = analyzer.process_file(file_info)
        
        if result['success']:
            # Salvar na pasta processados
            output_path = analyzer.save_results(file_info, result)
            print(f"✅ Salvo em: {output_path}")
            results.append(output_path)
        else:
            print(f"❌ Erro: {result['error']}")
    
    print(f"\n📊 Concluído! {len(results)} arquivo(s) processado(s)")
    print(f"📁 Resultados salvos na pasta 'processados/'")

if __name__ == "__main__":
    main()
```

## 🔧 **COMANDOS ESPECÍFICOS PARA DEEPSEEK:**

**Gere o código completo seguindo EXATAMENTE esta estrutura:**

1. **Imports e configurações iniciais**
2. **Classe FileInfo (dataclass)**  
3. **Classe OllamaClient** - usando `import ollama`
4. **Classe FileAnalyzer** - com método `save_results()` obrigatório
5. **Função main()** - interface CLI com salvamento automático
6. **Sistema de pastas** - criação automática de "analisar/" e "processados/"
7. **Nomenclatura de arquivos** - formato `YYYYMMDD_HHMMSS_nome.json`
8. **Tratamento de erros robusto**
9. **Logging profissional**
10. **Documentação completa**

## 📝 **REQUIREMENTS.TXT a incluir:**
```
ollama>=0.1.7
pandas>=1.5.0
python-pptx>=0.6.21
openpyxl>=3.1.0
```

## 🎯 **RESULTADO ESPERADO:**
- Código Python completo e funcional
- Sistema de salvamento automático na pasta "processados/"
- Arquivos com timestamp no nome (YYYYMMDD_HHMMSS_nome.json)
- Seguindo PEP8 e type hints
- Interface CLI intuitiva com feedback visual
- Logging detalhado
- Pronto para produção

**GERE O CÓDIGO COMPLETO AGORA COM SISTEMA DE SALVAMENTO OBRIGATÓRIO!**