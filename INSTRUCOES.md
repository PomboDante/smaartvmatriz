# Instalação da Smart Matrix v42

**Observação:** a conexão GitHub desta conversa retornou HTTP 403 ao tentar criar arquivos ou branches. Por isso, nada foi publicado no repositório automaticamente. Este pacote aplica a migração no seu clone local.

## Passo 1 — Ter um clone local do repositório

```bash
git clone https://github.com/PomboDante/smaartvmatriz.git
cd smaartvmatriz
```

## Passo 2 — Executar o migrador que está neste ZIP

Extraia o ZIP em outra pasta, depois execute (substitua o caminho indicado pelo local em que você extraiu o pacote):

```bash
python /caminho/para/smartv42_migration/migrate_smartv42.py .
```

No Windows, você pode usar `py` no lugar de `python` e informar o caminho completo para `migrate_smartv42.py`.

O script confere se `smartv41.py` corresponde à versão exata analisada, prepara os fragmentos, aplica as modificações e constrói `smartv42.py`. Os arquivos `smartv41.py` e `smartv37.py` não são alterados.

## Passo 3 — Validar antes de usar a máquina

```bash
python tools/build_smartv42.py --check
python -m py_compile smartv42.py
python -m unittest discover -s tests -v
python smartv42.py --simulado
```

Para executar os testes do projeto, instale antes `numpy` e `Pillow` se necessário: `python -m pip install numpy Pillow`.

## Passo 4 — Publicar e ativar o bot

```bash
git add smartv42.py smartv42_src tools/build_smartv42.py tests/test_smartv42.py .github/workflows/build-smartv42.yml SMARTV42_README.md
git commit -m "feat: SmartV42 modular com Z hibrido"
git push origin main
```

Após o push, o workflow do GitHub Actions executará os testes e reconstruirá `smartv42.py` automaticamente quando qualquer arquivo de `smartv42_src/` for modificado em `main`. Para permitir que o bot faça commits, verifique Settings > Actions > General > Workflow permissions > Read and write permissions.

Para futuras correções, edite apenas o módulo menor desejado e faça `git push`; o bot reconstruirá o arquivo final. Para testes locais, rode `python tools/build_smartv42.py`.

## Observações de funcionamento

- A interface permanece inspirada no UGS, com opção de importar `.gcode`, `.nc`, `.tap` e `.txt`.
- GRBL controla X/Y. O mecanismo Z usa GPIO e relés da Raspberry Pi.
- `Z1..Z8` e `M42 P1 ; Z_LEVEL=...` acionam os níveis. `G1 Z...` é rejeitado.
- A v42 preserva a janela da v41: X=246..270 mm e Y=-50..-29 mm. Recalibre se `calibracao_matriz.json` contiver valores antigos positivos de Y.
- O programa precisa ser testado em `--simulado` e depois com os atuadores desenergizados, antes de trabalhar no equipamento real.
