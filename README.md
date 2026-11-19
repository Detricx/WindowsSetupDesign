<div align="center">

# WinSetup

**Personalização do Windows, igual sempre que formato o computador.**

![Estado](https://img.shields.io/badge/estado-em_planeamento-orange?style=flat-square)
![Plataforma](https://img.shields.io/badge/Windows-10%20%7C%2011-0078D4?style=flat-square&logo=windows&logoColor=white)
![PowerShell](https://img.shields.io/badge/PowerShell-5.1%2B-5391FE?style=flat-square&logo=powershell&logoColor=white)

</div>

---

## 📖 Sobre o projeto

O **WinSetup** é um conjunto de scripts que aplica as minhas definições preferidas a uma instalação limpa do Windows. Em vez de repetir as mesmas configurações à mão depois de cada formatação, escolho o que quero num ficheiro de configuração, executo o programa e o sistema fica pronto.

Cada opção é **independente e opcional**: só é aplicado o que estiver ativado no `config.json`.

> ⚠️ **Estado atual:** projeto em fase de planeamento. As funcionalidades abaixo estão descritas como objetivo e vão sendo marcadas como concluídas no [Roadmap](#-roadmap).

---

## ✨ Funcionalidades

| Módulo | O que faz | Estado |
|:-------|:----------|:------:|
| **Barra de tarefas** | Alinhamento (esquerda/centro), ocultar pesquisa, widgets, vista de tarefas e chat | 🔲 |
| **Menu de contexto** | Repõe o menu clássico (completo) do botão direito e permite desativar entradas que não uso | 🔲 |
| **Remoção de apps** | Remove aplicações pré-instaladas (Paint, Clipchamp, Solitaire, etc.) a partir de uma lista editável | 🔲 |
| **Instalação de software** | Instala os programas que escolho de forma automática (via `winget`) | 🔲 |
| **Pesquisa** | Desativa os resultados do Bing na pesquisa do Menu Iniciar | 🔲 |
| **Segurança** | Cria ponto de restauro e backup do registo antes de alterar qualquer coisa | 🔲 |

---

## 🧩 Como funciona

O comportamento é controlado por um único ficheiro, o `config.json`. Exemplo:

```json
{
  "createRestorePoint": true,

  "taskbar": {
    "enabled": true,
    "alignment": "left",
    "hideSearch": true,
    "hideWidgets": true,
    "hideTaskView": true
  },

  "contextMenu": {
    "enabled": true,
    "useClassicMenu": true
  },

  "debloat": {
    "enabled": true,
    "removeApps": [
      "Microsoft.Paint",
      "Microsoft.MicrosoftSolitaireCollection",
      "Clipchamp.Clipchamp",
      "Microsoft.BingNews",
      "Microsoft.BingWeather"
    ]
  },

  "search": {
    "enabled": true,
    "disableBing": true
  },

  "apps": {
    "enabled": true,
    "install": [
      "Mozilla.Firefox",
      "7zip.7zip",
      "VideoLAN.VLC",
      "Microsoft.VisualStudioCode",
      "Git.Git"
    ]
  }
}
```

Para desativar um módulo, basta pôr `"enabled": false`.

---

## 🚀 Utilização

1. Faz download ou clona o repositório:
   ```powershell
   git clone https://github.com/DetricX/WinSetup.git
   cd WinSetup
   ```
2. Edita o `config.json` com as opções pretendidas.
3. Abre o **PowerShell como Administrador** e permite a execução de scripts apenas nesta sessão:
   ```powershell
   Set-ExecutionPolicy -Scope Process -ExecutionPolicy Bypass
   ```
4. Faz primeiro uma simulação, que mostra o que seria alterado sem aplicar nada:
   ```powershell
   .\setup.ps1 -Config .\config.json -DryRun
   ```
5. Se estiver tudo certo, executa de verdade:
   ```powershell
   .\setup.ps1 -Config .\config.json
   ```

Algumas alterações (barra de tarefas, menu de contexto) só ficam visíveis depois de reiniciar o Explorador de Ficheiros ou o computador.

---

## 🗂️ Estrutura prevista

```
WinSetup/
├── setup.ps1            # Ponto de entrada
├── config.json          # Opções escolhidas
├── modules/
│   ├── taskbar.ps1
│   ├── context-menu.ps1
│   ├── debloat.ps1
│   ├── search.ps1
│   └── apps.ps1
├── undo/                # Ficheiros .reg para reverter alterações
└── README.md
```

---

## 🔧 Notas técnicas

Exemplos das alterações ao registo que o projeto vai aplicar (todas em `HKEY_CURRENT_USER`, ou seja, só afetam o utilizador atual):

**Desativar pesquisa Bing no Menu Iniciar**

```reg
Windows Registry Editor Version 5.00

[HKEY_CURRENT_USER\SOFTWARE\Microsoft\Windows\CurrentVersion\Search]
"BingSearchEnabled"=dword:00000000
```

**Alinhar a barra de tarefas à esquerda (Windows 11)**

```reg
Windows Registry Editor Version 5.00

[HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Explorer\Advanced]
"TaskbarAl"=dword:00000000
```

**Menu de contexto clássico (Windows 11)**

```reg
Windows Registry Editor Version 5.00

[HKEY_CURRENT_USER\Software\Classes\CLSID\{86ca1aa0-34aa-4e8b-a509-50c905bae2a2}\InprocServer32]
@=""
```

### Limitações conhecidas

- No Windows 11, a barra de tarefas só suporta nativamente alinhamento **à esquerda** ou **ao centro**. Alinhar à direita exige software de terceiros.
- Ocultar entradas individuais do menu de contexto depende de cada aplicação (cada uma regista as suas extensões de forma diferente), por isso será feito caso a caso.
- Algumas apps do sistema (como o Microsoft Edge) estão protegidas e não podem ser removidas de forma segura.
- Grandes atualizações do Windows podem repor algumas definições.

---

## 🛡️ Segurança

- Todas as alterações são feitas no registo do **utilizador atual**, sem tocar em ficheiros do sistema.
- É criado um **ponto de restauro** antes de aplicar qualquer alteração.
- O modo `-DryRun` permite rever tudo antes de executar.
- Cada módulo tem o respetivo ficheiro em `undo/` para reverter as alterações.
- O código é aberto e legível. Recomenda-se ler os scripts antes de os executar, e testar primeiro numa máquina virtual.

---

## 🗺️ Roadmap

- [ ] Estrutura base e leitura do `config.json`
- [ ] Ponto de restauro e backup do registo
- [ ] Modo `-DryRun`
- [ ] Módulo da barra de tarefas
- [ ] Módulo do menu de contexto
- [ ] Módulo de remoção de apps
- [ ] Módulo de instalação de software (`winget`)
- [ ] Módulo de pesquisa
- [ ] Menu interativo para escolher opções sem editar o JSON
- [ ] Ficheiros de reversão (`undo/`)
- [ ] Novas opções: tema escuro, mostrar extensões de ficheiros, mostrar ficheiros ocultos, privacidade e telemetria

---

## 📄 Licença

Distribuído sob a licença MIT. Consulta o ficheiro `LICENSE` para mais informações.

---
