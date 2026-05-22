## Ambiente
- Windows Host
- Máquina virtual Kali Linux
- Serviço SSH local
- Ambiente Loopback/Laboratorial
- Captura e análise realizadas no Wireshark
- Comunicação TCP direcionada à porta 22

## Simulação
<img width="284" height="72" alt="image" src="https://github.com/user-attachments/assets/f56d9dbd-185b-4e5d-bbb9-227f7fa176af" />
Inicialização do serviço SSH na máquina local para simulação do vetor de ataque e recepção de conexões.

<img width="492" height="157" alt="image" src="https://github.com/user-attachments/assets/a700f3c9-351d-4ebe-9550-83b3f2ddb3bb" />

Preparação e execução do ataque por dicionário. Criação das wordlists de usuários (usuarios.txt) e senhas (senhas.txt) utilizando quebras de linha (\n), seguida pelo disparo da ferramenta Hydra contra o serviço SSH local (127.0.0.1).

<img width="839" height="180" alt="image" src="https://github.com/user-attachments/assets/07bcf102-125a-4e59-ac1b-bac7a2bd03ad" />
A execução bem –sucedida do ataque de dicionário com Hydra. O sublinhado aponta a identificação exata das credenciais válidas (login:kali/password:kali)para o serviço SSH local em apenas 4 segundos. 
## Evidências e Análise
## Filtro SSH
<img width="1256" height="139" alt="filtro ssh" src="https://github.com/user-attachments/assets/49dfc406-2a95-4836-a66a-6aab7d6c5da8" />

Os destaques em vermelho evidenciam o padrão clássico de um ataque automatizado de força bruta (brute force): requisições sequenciais enviadas em intervalos de milisegundos e a assinatura da biblioteca libssh utilizada pela ferramenta Hydra para realizar o disparo de tentativas. 

## Handshake TCP
<img width="875" height="142" alt="ack syn" src="https://github.com/user-attachments/assets/2307dd8d-314c-49d3-82d9-550f5209f10b" />
Tráfego filtrado por ‘tcp.flags.syn ==1 && tcp.flags.ack==’. Os destaques em vermelho evidenciam o comportamento agressivo do Hydra, que inicia múltiplas conexões em paralelo utilizando portas de origem dinâmicas e sequenciais em intervalos de frações de milisegundos direcionadas á porta 22. 

## Banner Grabbing
<img width="821" height="496" alt="tcp stream ssh" src="https://github.com/user-attachments/assets/18c03367-55eb-4faa-aa7c-05d2735a6007" />
Análise do fluxo de dados (Follow TCP Stream) de uma das tentativas de conexão.Os destaques em vermelho evidenciam a fase de “Banner Grabbing”, onde é possível visualizar em texto claro a identificação da ferramenta atacante (libssh) e a resposta do servidor informando sua versão exata do serviço (OpenSSH 10.3p1 Debian). 

## Portas Dinâmicas
<img width="792" height="222" alt="detalhes ssh" src="https://github.com/user-attachments/assets/3f7ac122-4de0-4dac-b13c-8dc9a8c2284e" />

Detalhes da camada tcp no wireshark . O destaque mapeia a porta de origem dinâmica aleatórioa (‘Source Port 36236’) aberta pelo Hydra para disparar as requisições , e o alvo fixado na porta padrão do serviço ('Destination Port:22') .

## Assinatura SSH do Hydra
<img width="352" height="64" alt="ssh protocol" src="https://github.com/user-attachments/assets/350f4d7e-f3d5-4490-9565-993f39b01788" />

Campo de aplicação identificando a versão do protocolo . O sublinhado aponta para o parâmetro onde o Hydra expõe sua assinatura digital através da biblioteca interna ‘libssh_0.11.3’. 

## Hex Dump
<img width="679" height="115" alt="Hexa decimal ssh protocol 2" src="https://github.com/user-attachments/assets/0a6e7eef-0b90-4bc6-b753-8c97d2a587aa" />
Painel de dump hexadecimal e tradução em caracteres ASCII. O bloco selecionado em azul destaca a representação dos bytes em memória que compõe o Indicador de Comprometimento (IOC) estudado, amarrando a análise de rede ao dado bruto binário.

## IOCs

- Múltiplas conexões SSH em sequência
- Tentativas rápidas de autenticação
- User-Agent/libssh
- Portas efêmeras sequenciais
- Destino TCP/22
- Frequência anômala de SYN

## Mitigações

- Implementação de MFA
- Fail2Ban
- Rate limiting
- Bloqueio após múltiplas falhas
- Restrição de acesso SSH
- IDS/IPS para brute force



