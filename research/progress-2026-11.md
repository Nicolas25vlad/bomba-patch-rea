# Bomba Patch REA — progresso da investigação

**Estado em 2026-11-23.** Pesquisa independente sobre uma imagem local, sem distribuir arquivos do jogo.

## Identificação e integridade

- Imagem fonte local: `bomba_patch.iso`; SHA-256 `4c61e679033c4ddf3275b9032d224a1b4c2693d1fd2a8a213387b6efa11547e3`.
- `SYSTEM.CNF`: `SLPM_663.74`, versão `1.02`, NTSC. O serial e a versão coincidem com o lançamento japonês de *World Soccer Winning Eleven 10* no [Redump](http://redump.org/disc/17280). Isso identifica o jogo-base provável; não prova que a imagem modificada seja idêntica ao disco original nem revela a linhagem do patch.
- ELF local `SLPM_663.74`: SHA-256 `8ffbfdcbe30d0cda3de54a8fab9ea238bdbb44df10e5684d42b5c6a66b77ab6b`; ELF32 little-endian, flags de Emotion Engine (`5900`, `eabi64`, `mips3`), entry point `0x100008`.

## Arquivos e formatos

- `MODULE.AFS` (SHA-256 `558f2127486432cf03bc7d7bdbe01c6917497eca6d401e79fb711dc4f2151ac5`): tabela verificada de 12 entradas. As 12 são ELF32 MIPS-I, tipo IOP de PS2; os metadados `.iopmod` incluem nomes como `padman`, módulos de memory card, áudio, USB e CD/DVD. Não são o código de gameplay do Emotion Engine.
- `BOOT.AFS` (SHA-256 `fbef6b39eaf1ae0daed5958c8eee7d64aea87cc65faf4f2bd2b3ab85a04e72f5`): sete entradas; os streams das entradas 4–6 foram validados como zlib, com tamanhos descomprimidos coerentes. Os tipos exatos desses dados permanecem desconhecidos.
- O layout externo dos dois arquivos foi cruzado com o script comunitário [winning_eleven_10.bms](https://aluigi.altervista.org/bms/winning_eleven_10.bms), não uma especificação oficial. Na entrada 0 de `BOOT.AFS`, `MWo3` e `bootset.ovl` são correspondências observadas, mas não identificam o formato completo. Uma busca direcionada não encontrou confirmação específica para WE10.

## Executável e limite atual

- `readelf -SW` mostrou 45 cabeçalhos de seção, sem `.mdebug`, `.debug_*`, `.pdr` ou seções de relocação; `.symtab` e `.strtab` são vazias. `.comment` declara `MW MIPS C Compiler (2.4.1.01)` e `PlayStation2`.
- LLVM 23.1.3 reconhece `r5900`, mas a decodificação local é parcial e deixa instruções como `<unknown>`. O provedor REA disponível rejeitou a arquitetura. PCSX2 e outros disassemblers PS2 consultados não estão instalados.
- **Não há conclusões sobre movimentação, física da bola ou animações.** Nomes de módulos e strings não foram tratados como prova de comportamento.

## Próxima etapa

O repositório [Ghidra Emotion Engine: Reloaded](https://github.com/chaoticgd/ghidra-emotionengine-reloaded) publica a release [v2.1.38](https://github.com/chaoticgd/ghidra-emotionengine-reloaded/releases/tag/v2.1.38), que declara suporte ao Ghidra 12.1.4 instalado e inclui suporte ao R5900/Emotion Engine. A instalação user-scoped foi autorizada; ainda precisa ser baixada, verificada pelo SHA-256 publicado e testada contra `SLPM_663.74` antes de qualquer conclusão de gameplay.

## Limites de publicação

A ISO, o executável e os arquivos extraídos permanecem locais, sob `work/` ignorado pelo Git. Este relatório contém apenas hashes, metadados estruturais, conclusões limitadas e referências; nenhum binário, asset, pixel, áudio ou trecho substancial de código proprietário foi incluído. O executável não foi iniciado e a ISO não foi modificada.
