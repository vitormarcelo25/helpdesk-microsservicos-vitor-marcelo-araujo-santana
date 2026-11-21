# Roteiro do vídeo (uns 5 minutos)

**Objetivo:** mostrar o que eu mudei e melhorei no projeto, falando enquanto mostro a tela.
Fale com as suas palavras. As frases abaixo em *itálico* são só um exemplo do que dizer.

**Antes de gravar (2 minutos):**
1. Abra o terminal na pasta do projeto e rode `docker compose up --build` (deixe subir).
2. Abra o navegador em http://localhost:8000.
3. Deixe o README aberto numa aba.
4. Comece a gravar a tela com o microfone ligado.

---

## Parte 1: O que eu fiz (1 min)

**Mostre:** o diagrama do README.

*"Este era um sistema de chamados de TI que funcionava como um programa só. Eu dividi em serviços pequenos:
usuários, inventário, chamados e notificações. Na frente deles tem um gateway, que é a única porta de entrada.
Cada serviço tem o seu banco de dados e os seus testes."*

## Parte 2: Como os serviços conversam (1 min)

**Mostre:** o arquivo `chamados/app/clientes.py`.

*"Antes, o chamado chamava o usuário como uma função normal. Agora é uma chamada pela rede. Por isso coloquei
um limite de tempo de 2 segundos e um disjuntor: se o serviço falhar 3 vezes seguidas, o sistema para de tentar
por 10 segundos e responde um erro claro, em vez de travar."*

**Mostre (rápido):** `chamados/app/eventos.py`.

*"As notificações funcionam por evento: o chamado avisa e segue em frente, sem esperar."*

## Parte 3: O sistema funcionando (1 min)

**Mostre:** na tela do sistema, entre com `carla@empresa.com` e a senha `Helpdesk2026`.
Abra um chamado, atribua a um técnico e resolva.

*"Em cima aparecem os 4 serviços em verde. Ao abrir um chamado, a notificação chega sozinha."*

## Parte 4: As melhorias novas (1 min 30 s)

**Mostre uma por vez, na tela:**

1. **Busca:** digite `impressora` no campo de busca. A lista filtra sozinha. Escolha também um status no filtro ao lado.
   *"Antes só dava para ver a lista inteira. Agora dá para buscar pelo título ou pela descrição."*
2. **Painel:** aponte para os números no topo e para as etiquetas coloridas de prioridade.
   *"Adicionei o indicador de chamados críticos abertos e a contagem por prioridade, para ver o que é urgente."*
3. **Tema escuro:** clique no botão da lua/sol no topo.
   *"Agora dá para trocar entre tema claro e escuro, e o navegador lembra a escolha."*

*"Cada melhoria tem teste automatizado."*

## Parte 5: Se um serviço cair (45 s)

**A ideia:** desligar um serviço de propósito e mostrar que o sistema não quebra inteiro.
O serviço que vamos desligar é o **Inventário** (o que guarda a lista de equipamentos).

**Passo a passo (deixe o terminal e o navegador lado a lado):**

1. No terminal, rode: `docker compose stop inventario`
2. No navegador, espere uns 5 segundos. No topo, o botão **Inventário** fica **vermelho**. Os outros continuam verdes.
   *"Desliguei o Inventário. Ele ficou vermelho, mas os outros continuam no ar."*
3. Em "Abrir chamado", deixe o campo **Equipamento** em **Nenhum** e clique em **Abrir chamado**.
   O chamado é criado normalmente.
   *"Sem equipamento, o chamado abre normal, porque ele não precisa do Inventário."*
4. Agora escolha um equipamento da lista e clique em **Abrir chamado** de novo.
   Aparece uma **mensagem de erro em vermelho** (embaixo da tabela) dizendo que o serviço está indisponível.
   *"Com equipamento, ele precisa do Inventário, que está fora. O sistema responde um erro claro, rápido, sem travar."*
5. Feche com a frase: *"No sistema antigo, um problema assim derrubava tudo. Agora a falha fica isolada."*
6. No terminal, religue: `docker compose start inventario` (em uns segundos o botão volta a ficar verde).

## Parte 6: Testes e conclusão (45 s)

**Faça no terminal:** `cd chamados` e depois `.venv\Scripts\python -m pytest -q` (mostre os testes passando).

Termine com 3 frases curtas (pode falar do seu jeito):

*"O lado bom: se um serviço quebra, o resto continua funcionando, e eu posso mexer em um serviço sem mexer nos outros.
O lado ruim: ficou mais trabalhoso, porque agora são várias peças para subir e configurar, e não uma só.
No geral, valeu a pena para entender como um sistema maior é organizado."*

---

## Trechos extras (uns 2 minutos): o que o professor pede e faltava

O professor pede estes itens no vídeo. Se o seu vídeo principal não mostra, grave **só estes trechos**
(sem editar) e envie como um segundo vídeo, ou regrave tudo. Somando os dois, fique entre 4 e 6 minutos.

### Extra A: subir tudo e ver os serviços (30 s)

No terminal, na pasta do projeto:

```powershell
docker compose ps
```

*"Subi tudo com um comando só, `docker compose up --build`. Aqui estão os serviços: usuários, inventário,
chamados, notificações, redis e o gateway. Todos saudáveis. Só o gateway tem porta aberta, a 8000."*

### Extra B: segurança (45 s)

1. Chamada **sem token** (deve mostrar `401`):

```powershell
curl.exe -i http://localhost:8000/chamados
```

*"Sem estar logado, o sistema recusa: erro 401."*

2. Entre como **Ana** (solicitante) e tente ver o chamado de **outra pessoa** (o 2 é do Bruno; deve dar `404`):

```powershell
$t = (Invoke-RestMethod -Method Post -Uri http://localhost:8000/auth/login -ContentType "application/json" -Body '{"email":"ana@empresa.com","senha":"Helpdesk2026"}').access_token
curl.exe -i -H "Authorization: Bearer $t" http://localhost:8000/chamados/2
```

*"A Ana é solicitante. Ela só enxerga os chamados dela. O chamado do Bruno nem aparece para ela."*

### Extra C: bancos separados (30 s)

```powershell
docker volume ls
docker compose exec chamados ls /dados
docker compose exec usuarios ls /dados
```

*"Cada serviço tem o seu próprio volume e o seu próprio arquivo de banco. O chamado não lê o banco do usuário:
ele pergunta ao serviço de usuários pela rede."*

### Extra D: fechar o chamado (15 s)

Na tela do sistema, no chamado que você resolveu, mude o status para **fechado**.

*"Então o fluxo completo é: abrir, atribuir, resolver e fechar."*

---

## Lembretes
- Se o vídeo passar de 6 minutos, pule a Parte 2 (o arquivo `eventos.py`).
- Se errar uma frase, continue falando. Não precisa editar.
- Publique no YouTube como **não listado** (ou Drive com acesso por link) e cole o link na linha 3 do README.
