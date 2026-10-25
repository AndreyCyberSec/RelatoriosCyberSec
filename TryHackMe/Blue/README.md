# TryHackMe — Blue | Write-up

## 1. Visão geral

- **Plataforma:** TryHackMe
- **Sala:** Blue
- **Área:** Segurança ofensiva / Pentest
- **Ferramentas:** Nmap, Metasploit Framework e CrackStation
- **Alvo:** Máquina Windows disponibilizada no laboratório
- **Objetivo:** Identificar uma vulnerabilidade, obter acesso inicial ao sistema, realizar atividades de pós-exploração e localizar as flags da sala.

> **Aviso:** Este relatório documenta atividades realizadas em um laboratório educacional autorizado. As técnicas descritas não devem ser utilizadas contra sistemas sem autorização explícita.

## 2. Reconhecimento e varredura de portas

A primeira etapa foi realizar uma varredura com o Nmap para identificar portas abertas, detectar versões dos serviços e procurar possíveis vulnerabilidades conhecidas.

### Comando utilizado

```bash
nmap -sV -vv --script vuln <IP_DA_MAQUINA>
```

### Parâmetros

- `-sV`: tenta identificar as versões dos serviços encontrados.
- `-vv`: aumenta o nível de detalhamento da saída.
- `--script vuln`: executa scripts NSE relacionados à identificação de vulnerabilidades conhecidas.

### Portas identificadas

Durante a varredura, foram registradas as seguintes portas TCP abertas:

| Porta | Observação |
|---|---|
| 135 | Associada ao RPC do Windows |
| 139 | NetBIOS Session Service |
| 445 | SMB sobre TCP |
| 3389 | Geralmente associado ao RDP |
| 49152 | Porta dinâmica, frequentemente utilizada por RPC |
| 49153 | Porta dinâmica, frequentemente utilizada por RPC |
| 49154 | Porta dinâmica, frequentemente utilizada por RPC |
| 49155 | Porta dinâmica, frequentemente utilizada por RPC |
| 49175 | Porta dinâmica, frequentemente utilizada por RPC |

As portas 139 e 445 chamaram a atenção por estarem associadas ao SMB e aos serviços de compartilhamento de arquivos do Windows.

**Limitação:** a saída completa do Nmap não foi salva durante a execução original. Portanto, não foi possível preservar neste relatório todos os detalhes da detecção de serviços e dos resultados dos scripts NSE.

A presença de uma porta aberta não comprova, isoladamente, a existência de uma vulnerabilidade.

## 3. Identificação da vulnerabilidade SMB — MS17-010

Durante a investigação, foi identificado um resultado relacionado ao script `smb-vuln-ms17-010`, utilizado para verificar indícios da vulnerabilidade conhecida como MS17-010.

O MS17-010 é um boletim de segurança da Microsoft que abrange vulnerabilidades relacionadas ao SMBv1. O EternalBlue é uma técnica de exploração associada ao CVE-2017-0144, integrante dessa família de vulnerabilidades.

A identificação de uma possível vulnerabilidade no SMB orientou a próxima etapa da investigação: verificar, no ambiente de laboratório, se era possível obter acesso inicial à máquina por meio do Metasploit Framework.

**Referências técnicas:**

- [Microsoft Security Bulletin MS17-010](https://learn.microsoft.com/en-us/security-updates/securitybulletins/2017/ms17-010)
- [CVE-2017-0144](https://nvd.nist.gov/vuln/detail/CVE-2017-0144)

**Limitação:** como a saída completa do Nmap não foi preservada, este relatório não inclui a mensagem original retornada pelo script de detecção.

## 4. Exploração com Metasploit Framework

Após identificar indícios de uma possível vulnerabilidade relacionada ao SMB, iniciei o Metasploit Framework e pesquisei módulos associados ao MS17-010.

### Pesquisa do módulo

```text
search ms17
```

O módulo selecionado foi:

```text
exploit/windows/smb/ms17_010_eternalblue
```

O payload utilizado foi:

```text
windows/x64/shell/reverse_tcp
```

O objetivo era estabelecer uma conexão reversa com a máquina-alvo e obter acesso inicial ao sistema.

### Resultado observado

Consegui estabelecer um shell remoto por meio do módulo de exploração SMB.

Essa primeira sessão fornecia um shell, mas ainda não oferecia os recursos de uma sessão Meterpreter. Por esse motivo, foi necessário realizar uma etapa adicional para converter a sessão e continuar a investigação.

A obtenção do shell representou o acesso inicial ao sistema no contexto do laboratório.

## 5. Conversão do shell para Meterpreter

Após obter o shell reverso, utilizei o módulo de pós-exploração:

```text
post/multi/manage/shell_to_meterpreter
```

A sessão estabelecida anteriormente foi utilizada como origem da conversão.

O objetivo dessa etapa foi obter uma sessão Meterpreter, que oferece recursos adicionais para interagir com o sistema remoto e realizar atividades de pós-exploração.

### Resultado observado

A conversão foi concluída com sucesso. Com a sessão Meterpreter, consegui continuar a investigação da máquina, inspecionar processos e procurar as flags solicitadas pela sala.

Essa etapa demonstrou a diferença entre obter um shell remoto e utilizar uma sessão Meterpreter para realizar atividades adicionais de investigação.

## 6. Inspeção de processos e migração da sessão

Com a sessão Meterpreter estabelecida, utilizei o comando `ps` para listar os processos em execução na máquina:

```text
ps
```

Durante a análise, identifiquei o processo associado ao serviço Print Spooler do Windows, normalmente representado por `spoolsv.exe`.

Em seguida, utilizei o identificador do processo (PID) correspondente para tentar migrar a sessão Meterpreter para esse processo.

### Resultado observado

A migração fez parte das atividades de pós-exploração realizadas no laboratório e permitiu praticar a interação com processos do Windows por meio do Meterpreter.

É importante distinguir a migração de processo da elevação de privilégios. Migrar uma sessão para outro processo não comprova, por si só, que o nível de privilégio foi aumentado. Para confirmar uma elevação, seria necessário registrar evidências do contexto de segurança antes e depois da operação.

## 7. Obtenção e análise de hashes de contas

Durante a etapa de pós-exploração, utilizei o recurso `hashdump` do Meterpreter para obter hashes de contas locais do Windows.

### Resultado observado

Consegui obter hashes associados à conta Administrator e a outro usuário da máquina.

Em seguida, utilizei a plataforma CrackStation para verificar se as senhas correspondentes podiam ser recuperadas a partir dos hashes obtidos.

Consegui recuperar as senhas correspondentes aos hashes analisados.

### Aprendizado

Hashes de senha não são senhas em texto puro. Dependendo do tipo de hash, da complexidade da senha e dos recursos disponíveis, pode ser possível encontrar uma correspondência e recuperar a senha original.

Essa etapa demonstrou a importância de proteger as credenciais locais e de utilizar senhas fortes.

> Por segurança, este relatório não inclui hashes nem senhas recuperadas.

## 8. Localização e leitura das flags

Após concluir as etapas anteriores, utilizei os recursos do Meterpreter para procurar os arquivos de flag na máquina-alvo.

### Pesquisa dos arquivos

O comando utilizado foi:

```text
search -f nome_da_flag.txt
```

A pesquisa permitiu localizar os arquivos correspondentes. Com os caminhos identificados, utilizei o comando `cat` para ler o conteúdo dos arquivos de texto.

### Resultado observado

Consegui localizar os arquivos de flag e consultar seu conteúdo por meio da sessão Meterpreter, concluindo os objetivos práticos da sala TryHackMe — Blue.

As flags não foram incluídas neste relatório para manter o foco na documentação do processo e evitar divulgar respostas diretas da sala.

## 9. Conclusão

A resolução da sala permitiu praticar diferentes etapas de uma investigação de segurança em ambiente controlado:

1. Reconhecimento de portas e serviços com Nmap.
2. Investigação de uma possível vulnerabilidade relacionada ao SMBv1.
3. Utilização do Metasploit Framework para obter acesso inicial por meio de um shell reverso.
4. Conversão da sessão de shell para Meterpreter.
5. Inspeção de processos e migração da sessão.
6. Obtenção e análise de hashes de contas locais.
7. Pesquisa de arquivos e leitura das flags.

A principal aprendizagem foi compreender que uma investigação de segurança envolve várias etapas. Identificar um serviço exposto é apenas o começo: é necessário analisar os indícios, validar a hipótese no ambiente autorizado e documentar os resultados de cada fase.

Também foi possível compreender melhor a diferença entre acesso inicial, pós-exploração, migração de processo e elevação de privilégios.

Este write-up faz parte do meu processo de aprendizado prático em cibersegurança. Pretendo continuar aprimorando a análise técnica, a documentação das atividades e a qualidade das evidências utilizadas nos meus relatórios.

## 10. Limitações e evidências

As capturas de tela e a saída completa do Nmap não foram preservadas durante a execução original.

Por esse motivo, algumas informações foram documentadas com base nas anotações feitas durante a resolução da sala. Os resultados descritos como observados correspondem ao que registrei durante a atividade, mas não estão acompanhados de evidências visuais originais.

Em futuras atividades, pretendo preservar as saídas relevantes das ferramentas, registrar as etapas importantes e incluir evidências que sustentem as conclusões apresentadas.

## 11. Referências

- [TryHackMe — Blue](https://tryhackme.com/room/blue)
- [Nmap Reference Guide](https://nmap.org/book/man.html)
- [Microsoft Security Bulletin MS17-010](https://learn.microsoft.com/en-us/security-updates/securitybulletins/2017/ms17-010)
- [NVD — CVE-2017-0144](https://nvd.nist.gov/vuln/detail/CVE-2017-0144)
- [Metasploit Documentation](https://docs.metasploit.com/)
- [CrackStation](https://crackstation.net/)
