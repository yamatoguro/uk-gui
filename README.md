# uk-gui

Painel multiplataforma do **UniversalKeyboard**: gerencia o driver, define o que aparece
nas telas das 112 teclas e edita macros e layouts. Referência conceitual: o que o **VIA**
faz pelo QMK — mas aqui o alvo é um teclado com tela por keycap e o layout também responde
ao sistema operacional do host.

## O que faz

1. **Detecta o layout ativo do SO do host** e o envia ao teclado pela interface HID
   vendor-specific.
2. **Fingerprint da máquina** (MachineGuid / machine-id / IOPlatformUUID) para lembrar o
   perfil por computador.
3. **Edita macros e layouts** e pré-visualiza as teclas com o mesmo modelo de dados que o
   teclado usa.
4. **Gerencia o driver** e o estado do dispositivo.

## Por que a detecção de layout vive aqui

Um teclado HID envia **usage IDs**, não caracteres. Quem traduz usage → caractere é o layout
ativo do SO. Essa informação não existe no protocolo HID — só um processo rodando no host,
com acesso à API de entrada, consegue obtê-la.

| SO | API |
|---|---|
| Windows | `GetKeyboardLayout` sobre a thread em foco; `WM_INPUTLANGCHANGE` |
| Linux X11 | `XkbGetState` / `xkbcommon` |
| Linux Wayland | dependente do compositor (não há API universal) |
| macOS | `TISGetInputSourceProperty` |

## Stack

**Ainda não decidida.** Requisitos declarados: próximo do kernel, baixo custo
computacional, extrema performance, multiplataforma. Candidatos: Rust, C++ ou C com toolkit
nativo.

A escolha precisa de ADR antes de qualquer código. Ver
[`../docs/context-map.md`](../docs/context-map.md) §5 e §12.

## Construir / testar

`[A VALIDAR]` — depende da stack. O CI detecta `Cargo.toml`, `CMakeLists.txt` ou
`package.json` e roda o build correspondente; sem stack definida, emite aviso explícito em
vez de fingir que compilou.

## Fronteira

- **Não** linka biblioteca de `uk-pi0` nem de `uk-sc-integration`.
- Fala com o teclado **somente** pela interface HID vendor-specific.
- **Não** é necessário para digitar: sem o painel, o teclado opera com o último perfil
  persistido.

## Estado

Fase 0 — definição de arquitetura.
