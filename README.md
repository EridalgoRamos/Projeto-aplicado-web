# 🛡️ Projeto Aplicado: Práticas de Mercado

![Status](https://img.shields.io/badge/Status-Produção-success)
![Segurança](https://img.shields.io/badge/Segurança-OWASP_Top_10-blue)
![Infraestrutura](https://img.shields.io/badge/Infraestrutura-Oracle_Cloud-red)
![CI/CD](https://img.shields.io/badge/CI%2FCD-GitHub_Actions-2088FF)

## 📌 Visão geral
Este projeto documenta o desenvolvimento, infraestrutura e automação de uma aplicação web segura, simulando um ambiente de produção real. O ciclo de vida da aplicação foi desenhado com foco nos princípios de **Secure by Design** e **Secure by Default**, garantindo proteção desde o código até ao servidor.

A arquitetura está dividida em três eixos técnicos perfeitamente integrados através de uma esteira de automação contínua.

---

## 🏗️ Arquitetura e eixos tecnológicos




### ☁️ Eixo 1: Infraestrutura Cloud & Segurança
Servidor alojado na **Oracle Cloud Infrastructure (Free Tier)**, configurado com foco em defesa em profundidade e princípio do menor privilégio.
* **Sistema Operativo:** Ubuntu Server.
* **Servidor Web:** Nginx (Proxy Reverso) integrado com Gunicorn via Unix socket.
* **Criptografia TLS/SSL:** Certificado Let's Encrypt gerado via Certbot para o domínio `163.176.126.198.nip.io`, com redirecionamento forçado de HTTP para HTTPS (Nota A no Qualys SSL Labs).
* **Hardening:** Autenticação por palavra-passe desativada (acesso exclusivo por chaves SSH).
* **Firewall (Fail2Ban):** Proteção ativa na porta 22, com regras rígidas de banimento de 24 horas após 4 tentativas de autenticação falhadas. Portas públicas restritas a 80 (HTTP) e 443 (HTTPS).

### 📦 Eixo 2: Controle de versão e gestão de código
Repositório centralizado no GitHub com práticas avançadas de proteção e gestão de segredos.
* **Prevenção de Fugas de Dados:** Ficheiro `.gitignore` rigorosamente configurado para impedir o rastreio de `.env`, chaves privadas, bases de dados locais e ficheiros de compilação.
* **Gestão de Segredos:** Credenciais de implantação geridas exclusivamente via *GitHub Secrets*, garantindo que nenhuma informação sensível transita no código.

### 💻 Eixo 3: Desenvolvimento da aplicação web
Protótipo de software seguro desenvolvido em Python/Flask, concebido com a assistência de Inteligência Artificial para revisão e refatoração de código.
* **Estrutura:** Módulo de autenticação seguro e *dashboard* de monitorização de logs de rede.
* **Funcionalidades:** Ecrã de Login, página interna protegida (restrita a utilizadores validados) e fluxo de Logout seguro com destruição de sessão.

---

## 🔄 Automação (CI/CD)



O fluxo de *deploy* está 100% automatizado via **GitHub Actions**. Quando um novo *commit* é enviado para o *branch* `main`, a pipeline é acionada:
1. Valida o ambiente de execução.
2. Estabelece ligação segura por SSH à instância da Oracle Cloud utilizando credenciais encriptadas.
3. Descarrega as atualizações do repositório, reinicia o ambiente virtual e os serviços em background, e aplica a versão sem tempo de inatividade.

---

## 🛡️ Mitigação de Vulnerabilidades (OWASP Top 10:2025)
A aplicação mitiga ativamente três categorias críticas do *Open Worldwide Application Security Project*:

1. **Broken Access Control (Falha de Controlo de Acesso)**
   * **Implementação:** Controlo de sessão restrito do lado do servidor. Tentativas de acesso direto à rota de logs por agentes não autenticados são bloqueadas na origem e redirecionadas. O botão de *logout* destrói o cookie de sessão imediatamente.

2. **Cryptographic Failures (Falhas Criptográficas)**
   * **Implementação:** Comunicação inteiramente cifrada via Nginx Proxy Reverso. Tráfego de texto limpo é redirecionado. Utilização de TLS 1.3 de última geração, prevenindo ataques de interceção (*Man-in-the-Middle*).

3. **Identification and Authentication Failures (Falhas de Autenticação)**
   * **Implementação:** Sanitização estrita de inputs no formulário de login contra injeções. Na camada de infraestrutura, a porta 22 (SSH) está blindada com Fail2Ban, erradicando a eficácia de ataques de força bruta (*Brute Force*).

---

## 📌 Nota de Engenharia: Criptografia Pós-Quântica (PQC)
A infraestrutura web atingiu o grau máximo de segurança clássica (**Nota A no SSL Labs**). O suporte à troca de chaves PQC (*Post-Quantum Cryptography*, ex: Kyber) não foi forçado no Nginx devido a uma limitação arquitetónica deliberada: a instância opera sobre a biblioteca OpenSSL 3.0.13 nativa do Ubuntu Server, que carece de suporte nativo integrado para curvas PQC experimentais. Evitou-se a recompilação manual do núcleo criptográfico no ambiente de produção para garantir a máxima estabilidade operacional e integridade dos serviços de base.
