# pingpong



Negoita Radu Florin
333AB



;program ce simuleaza un joc ping-pong
;controlul paletei se face cu ajutorul tastelor A si Z
;A-->miscare in sus
;Z-->miscare in jos




Macro SetPos X,Y
Mov Ah,02h             ;macro-uri pentru 
Mov Dh,X               ; pozitionarea cursorului
Mov Dl,Y
int 10h
endm

Macro Paleta
Mov Ah,02h
Mov Dl,0BAh
int 21h
Mov Al,0h
endm

Macro Limpiar
Mov Ah,02h
Mov Dl,00h
int 21h
endm

Macro Marco Horizontal
SetPos 1,1
Mov Ah,09h
Lea Dx,Horizontal
int 21h

SetPos 11,1
Mov Ah,09h
Lea Dx,Horizontal
int 21h
endm

Macro Marco2 Vertical
Mov Cx,11
For: SetPos Cl,00h
Mov Ah,09h
Lea Dx,Vertical
int 21h

SetPos Cl,0Eh
Mov Ah,09h
Lea Dx,Vertical
int 21h
Loop For
endm


Macro SetBall
SetPos FB,CB
endm

Macro Ball
Mov Ah,02h
Mov Dl,0A9h
int 21h
endm

Macro MarcadorI
SetPos 15,4
Mov Ah,02h
Mov Dl,puntos
Add Dl,30h
int 21h

SetPos 15,10
Mov Ah,02h
Mov Dl,puntos2
Add Dl,30h
int 21h
endm

.Model Small
.Data
.Code
.Stack

Msj db "GAAAAAAAAAARD $"
Msj2 db "*$"
FP1 db 06h
FP2 db 06h
FB db 05h
CB db 05h
LimS db 01h
LimI db 11
Bandera1 db 02h
Bandera2 db 00h
puntos db 00h
puntos2 db 00h
Msg1 db "Felicitari!!!!"
Msg2 db "Pierzator"

Paleta1 db 00h
Paleta2 db 01h
.StartUp

MarcadorI
JMP Iniciar

Ciclo:
Mov Cx,10
Mov Ah,01h 	  ;inregistreaza o apasare de tasta
int 16h

CMP Al,061h   ;comparatii cheie
Je Sube

CMP Al,7Ah
Je Baja

CMP Al,073h
Je Salir

JMP VerCB

Loop Ciclo

Iniciar:      ;afisarea paletelor si a chenarului
Marco Msj
Marco2 Msj2
SetPos FP1,01h
Paleta
SetPos FP2,0Dh
Paleta
SetBall
Ball
Mov Al,0

JMP Ciclo

noBuffer:
Mov Ah,00h  ;retine valoarea tastei apasate
int 16h

Mov Al,00h

JMP VerCB

Sube:
CMP FP1,2
Je noBuffer
SetPos FP1,01h
Limpiar
Sub FP1,01h

SetPos FP1,01h    ;miscari pentru paleta jucatorului
Paleta

JMP noBuffer

Baja:
CMP FP1,0Ah
Je noBuffer
SetPos FP1,01h
Limpiar
Add FP1,01h
SetPos FP1,01h
Paleta

JMP noBuffer

AD:
Mov Bl,FB
Add Bl,CB
CMP Bl,4h
Je flechaD

Mov Al,FB
Sub Al,01h
Mov Bl,LimS
CMP Al,Bl
Je ComingAD

SetPos FB,CB
Limpiar
Dec FB
Inc CB
SetPos FB,CB
Ball
Mov Bandera2,01h
JMP VerMarcador

AI: Mov Bl,FB
Add Bl,CB
CMP Bl,0Ch
Je flechaI

Mov Al,FB
Sub Al,01h
Mov Bl,LimS
CMP Al,Bl
Je ComingAI

SetPos FB,CB
Limpiar
Dec FB
Dec CB
SetPos FB,CB
Ball
Mov Bandera2,00h
JMP VerMarcador

BI: Mov Bl,FB
Add Bl,CB
CMP Bl,22d
Je flechaI

Mov Al,FB
Add Al,01h
Mov Bl,LimI
CMP Al,Bl
Je ComingBI

SetPos FB,CB
Limpiar
Inc FB
Dec CB
SetPos FB,CB
Ball
Mov Bandera2,01h
JMP VerMarcador

BD: Mov Bl,FB     ;directia in care se duce paleta robotului
Add Bl,CB
CMP Bl,0Ch
Je flechaD

Mov Al,FB
Add Al,01h
Mov Bl,LimI
CMP Al,Bl
Je ComingBD

SetPos FB,CB
Limpiar
Inc FB
Inc CB
SetPos FB,CB
Ball
Mov Bandera2,00h
JMP VerMarcador

ComingAD:
CMP Bandera1,0      ;avertizeaza de unde vine mingea
Je SumarBandera

ComingBD:
CMP Bandera1,01h
Je RestarBandera

ComingAI:
CMP Bandera1,02h
Je SumarBandera

ComingBI:
CMP Bandera1,03h
Je RestarBandera

SumarBandera:
Add Bandera1,01h
JMP Ciclo

RestarBandera:
Sub Bandera1,01h
JMP Ciclo

Rebote1:
Mov Al,FB
Mov Bl,FP1
CMP Al,Bl
Je RestaurarBandera    ;pentru a vedea daca mingea a trecut de limita
Jne Gol

Rebote2:Mov Al,FB
Mov Bl,FP2
CMP Al,Bl
Je RestaurarBandera2
Jne Gol2

RestaurarBandera:
Mov Ah,2Ch             ;luam timpul ca valoare aleatorie
int 21h

Mov Al,Dh
Mov Bl,5
Mul Bl
Mov Bl,100
Div Bl

CMP Al,2
Je Es5
Mov Bandera1,Al
JMP Comparaciones

RestaurarBandera2:
Mov Ah,2Ch
int 21h

Mov Al,Dh
Mov Bl,5
Mul Bl
Mov Bl,100
Div Bl

CMP Al,0
Je Es4

CMP Al,1
Je Es2

CMP Al,2
Je Es3

Es2: Mov Bandera1,02h
JMP Comparaciones

Es3: Mov Bandera1,03h
JMP Comparaciones

Es4: Mov Bandera1,04h
JMP Comparaciones

Es5: Mov Bandera1,05h
JMP Comparaciones

Comparaciones:
CMP Bandera1,00h
Je AD

CMP Bandera1,01h
Je BD

CMP Bandera1,02h
Je AI

CMP Bandera1,03h             ;directia mingii
Je BI

CMP Bandera1,04h
Je flechaI

CMP Bandera1,05h
Je flechaD

VerCB:                      ;directia mingii
Mov Al,CB
Sub Al,01h
CMP Al,01h
Je Rebote1

Mov Al,CB
Add Al,01h
CMP Al,0Dh
Je Rebote2


JMP Comparaciones

flechaI:
SetPos FB,CB
Limpiar
Sub CB,01h
SetPos FB,CB
Ball
Mov Bandera2,00h
JMP VerMarcador

flechaD:
SetPos FB,CB
Limpiar
Add CB,01h
SetPos FB,CB
Ball
Mov Bandera2,01h
JMP VerMarcador
Gol2:
SetPos 15,4
Add puntos2,01h
Mov Ah,02h
Mov Dl,puntos2
Add Dl,30h
int 21h
SetPos FB,CB
Limpiar
Mov FB,05h
Mov CB,05h
Mov Bandera1,03h
JMP Ganador

Gol:SetPos 15,10
Add puntos,01h
Mov Ah,02h
Mov Dl,puntos
Add Dl,30h
int 21h
SetPos FB,CB
Limpiar
Mov FB,05h
Mov CB,05h
JMP Ganador
                        ;calculam punctajul acestei metode
VerMarcador:            ;trecem la un nivel superior
Mov Al,puntos2
Sub Al,02h
CMP Al,puntos
Jne PaletaCPU
Je PaletaCPU2

PaletaCPU:
Mov Al,Bandera2
                        ;nivelul calculatorului
CMP Al,0
Je SubeCPU
Jne BajaCPU

SubeCPU:Mov Al,FP2
Sub Al,01h
CMP Al,LimS
Je BajaCPU

SetPos FP2,0Dh
Limpiar
Sub FP2,01h
SetPos FP2,0Dh
Paleta
JMP Ciclo

BajaCPU:Mov Al,FP2
Add Al,01h
CMP Al,LimI
Je SubeCPU

SetPos FP2,0Dh
Limpiar
Add FP2,01h
SetPos FP2,0Dh
Paleta
JMP Ciclo

PaletaCPU2:
SetPos FP2,0Dh
Limpiar
Mov Al,FB
Mov FP2,Al
SetPos FP2,0Dh
Paleta
JMP Ciclo

Ganador:
Mov Al,puntos
CMP Al,5
Je Gana1

Mov Al,puntos2
CMP Al,5
Je Gana2
Jne Iniciar

Gana1:                 ;mesajul castigatorului
Mov Ah,09h
Lea DX,Msg2
int 21h
JMP Salir

Gana2:  Mov Ah,09h
Lea DX,Msg1
int 21h
JMP Salir
Salir:
Mov Ah,00h              ;iesire din joc prin apasarea unei taste
.Exit



