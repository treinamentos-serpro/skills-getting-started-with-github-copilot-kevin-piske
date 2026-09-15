## Plano: Testes Backend FastAPI

Adicionar uma suíte `pytest` independente em `tests/`, declarando `pytest` em `requirements.txt`, usando `TestClient` e isolamento do estado global em memória. A cobertura deve proteger os contratos atuais da API, incluindo a prevenção de inscrições duplicadas e o novo fluxo de remoção, sem alterar a lógica de produção.

**Etapas**
1. Atualizar `requirements.txt` para declarar `pytest`, mantendo as dependências atuais.
2. Criar `tests/test_app.py` com `TestClient(app)` importado de `src.app`.
3. Criar uma fixture de isolamento que faça `deepcopy` de `src.app.activities` e restaure o dicionário após cada teste mutante, evitando dependência entre testes.
4. Adicionar testes para:
   - `GET /` retornar redirecionamento para `/static/index.html`.
   - `GET /activities` retornar `200` e as atividades esperadas.
   - `POST /activities/{activity_name}/signup` inscrever um participante válido.
   - `POST` em atividade inexistente retornar `404`.
   - `POST` para participante já inscrito retornar `400` e não duplicar o e-mail.
   - `DELETE /activities/{activity_name}/signup` remover participante existente.
   - `DELETE` para participante ausente retornar `404`.
   - `DELETE` em atividade inexistente retornar `404`.
5. Estruturar cada teste no padrão AAA, com comentários ou separação visual explícita:
   - **Arrange:** definir atividade, e-mail e estado inicial necessário.
   - **Act:** executar uma única chamada HTTP principal usando `client`.
   - **Assert:** verificar status, corpo da resposta e, quando relevante, o estado de `activities`.
6. Executar a coleta e a suíte completa; corrigir somente falhas relacionadas à estrutura dos testes ou aos contratos já implementados.

**Arquivos relevantes**
- `requirements.txt` — adicionar `pytest` como dependência de desenvolvimento/teste, seguindo o formato simples existente.
- `tests/test_app.py` — novo arquivo com os testes backend e a fixture de restauração de `activities`.
- `src/app.py` — somente referência aos endpoints existentes; não modificar durante esta tarefa.
- `pytest.ini` — reutilizar `pythonpath = .`; nenhuma alteração prevista.

**Verificação**
1. `python -m pytest --collect-only -q` deve coletar todos os testes sem erro de import.
2. `python -m pytest -q` deve passar a suíte completa.
3. Confirmar que a fixture deixa o estado inicial intacto entre testes mutantes, especialmente entre inscrição e remoção.

**Decisões**
- Usar `from src.app import app, activities` e `TestClient`, alinhado ao launcher `uvicorn src.app:app` existente.
- Restaurar o estado global com cópia profunda em vez de depender da ordem dos testes.
- Não incluir teste para `max_participants` como comportamento esperado, pois o endpoint atualmente não aplica esse limite; essa é uma lacuna funcional separada.
- Não criar banco, camada de serviço ou refatoração da API; o objetivo é cobertura backend mínima e confiável.

**Escopo**
- Incluído: dependência `pytest`, diretório separado `tests/`, testes dos endpoints atuais e isolamento do estado.
- Excluído: correções funcionais na API, testes de frontend, persistência permanente e alteração da documentação de execução.
