.586
.model flat, stdcall
option casemap: none

include C:\masm32\include\windows.inc
include C:\masm32\include\kernel32.inc
include C:\masm32\include\masm32.inc

includelib C:\masm32\lib\kernel32.lib
includelib C:\masm32\lib\masm32.lib

.stack 100h

NumbToStr   PROTO :DWORD,:DWORD

.data
not_supported_msg db "cpuid is not supported",13,10,0
stepping_label db "stepping number: ", 0
family_label db "family number: ", 0
func_num_label db "max number of functions: ", 0
newline db 13,10,0
bitmask_low dd 0fh
bitmask_byte dd 0ffh
manufacturer db 13 dup (0)
manufacturer_label db "manufacturer: ", 0

.data?
stepping dd ?
family dd ?
number_address dd ?
opnum dd ?
cpuid1 dd ?
buf db 11 dup (?)

.code
start:

;check for cpuid support
pushfd
pop eax
mov ebx, eax
xor eax, 200000h
push eax
popfd
cmp eax, ebx
je error

invoke StdOut, addr func_num_label

mov eax, 0
cpuid

mov dword ptr manufacturer, ebx
mov dword ptr manufacturer + 4, edx
mov dword ptr manufacturer + 8, ecx

mov opnum, eax
invoke NumbToStr, opnum, addr buf

invoke StdOut, eax
invoke StdOut, addr newline

invoke StdOut, addr manufacturer_label
invoke StdOut, addr manufacturer
invoke StdOut, addr newline

mov eax, 1
cpuid

mov cpuid1, eax
mov family, eax
and eax, bitmask_low
mov stepping, eax

invoke NumbToStr, stepping, addr buf
mov number_address, eax

invoke StdOut, addr stepping_label
invoke StdOut, number_address
invoke StdOut, addr newline

mov eax, family
shr eax, 8

and eax, bitmask_low
mov family, eax

cmp family, 0fh
jl print_family

mov eax, cpuid1
shr eax, 20
and eax, bitmask_byte
add family, eax

print_family:
invoke NumbToStr, family, addr buf
mov number_address, eax

invoke StdOut, addr family_label
invoke StdOut, number_address

jmp endprog

error:
invoke StdOut, addr not_supported_msg

endprog:
invoke ExitProcess, 0

NumbToStr PROC uses ebx x:DWORD, buffer:DWORD

    mov     ecx,buffer
    mov     eax,x
    mov     ebx,10
    add     ecx,ebx             ; ecx = buffer + max size of string
@@:
    xor     edx,edx
    div     ebx
    add     edx,48              ; convert the digit to ASCII
    mov     BYTE PTR [ecx],dl   ; store the character in the buffer
    dec     ecx                 ; decrement ecx pointing the buffer
    test    eax,eax             ; check if the quotient is 0
    jnz     @b

    inc     ecx
    mov     eax,ecx             ; eax points the string in the buffer
    ret

NumbToStr ENDP

end start
