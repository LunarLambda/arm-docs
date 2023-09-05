⚠️ This information is taken verbatim from [ARM Compiler armasm User Guide Version 5.06](https://developer.arm.com/documentation/dui0473/m/writing-arm-assembly-language/assembly-language-changes-after-rvct-v2-1).
While it applies more or less as-is to GNU as, the armasm documentation should *not* be used for writing assembly code targeting the GNU compiler toolchain.

To use unified assembler language (UAL) in GNU as, place the `.syntax unified` directive at the top of the file. To use it for inline assembly in C and C++, pass the `-masm-syntax-unified` option to GCC.

[GNU as documentation](https://sourceware.org/binutils/docs/as)\
[GCC ARM options](https://gcc.gnu.org/onlinedocs/gcc/ARM-Options.html)

**Changes from earlier ARM assembly language**

<table>
    <tr>
        <th align="left">Change</th>
        <th align="left">Pre-UAL ARM syntax</th>
        <th align="left">Preferred UAL syntax</th>
    </tr>
    <tr>
        <td>The default addressing mode for <code>LDM</code> and <code>STM</code> is <code>IA</code></td>
        <td><pre>LDMIA, STMIA</pre></td>
        <td><pre>LDM, STM</pre></td>
    </tr>
    <tr>
        <td>You can use <code>PUSH</code> and <code>POP</code> mnemonics for full, descending stack operations in ARM in addition to Thumb.</td>
        <td><pre>STMFD sp!, {<em>reglist</em>}&#13;LDMFD sp!, {<em>reglist</em>}</pre></td>
        <td><pre>PUSH {<em>reglist</em>}&#13;POP {<em>reglist</em>}</pre></td>
    </tr>
    <tr>
        <td>You can use the <code>LSL</code>, <code>LSR</code>, <code>ASR</code>, <code>ROR</code> and <code>RRX</code> instruction mnemonics for instructions with rotations and no other operation, in ARM in addition to Thumb.</td>
        <td><pre>MOV <em>Rd</em>, <em>Rn</em>, LSL <em>shift</em>&#13;MOV <em>Rd</em>, <em>Rn</em>, LSR <em>shift</em>&#13;MOV <em>Rd</em>, <em>Rn</em>, ASR <em>shift</em>&#13;MOV <em>Rd</em>, <em>Rn</em>, ROR <em>shift</em>&#13;MOV <em>Rd</em>, <em>Rn</em>, RRX</pre></td>
        <td><pre>LSL <em>Rd</em>, <em>Rn</em>, <em>shift</em>&#13;LSR <em>Rd</em>, <em>Rn</em>, <em>shift</em>&#13;ASR <em>Rd</em>, <em>Rn</em>, <em>shift</em>&#13;ROR <em>Rd</em>, <em>Rn</em>, <em>shift</em>&#13;RRX <em>Rd</em>, <em>Rn</em></pre></td>
    </tr>
    <tr>
        <td>Use the <code><em>label</em></code> form for PC-relative addressing. Do not use the <code><em>offset</em></code> form in new code.</td>
        <td><pre>LDR <em>Rd</em> [pc, #<em>offset</em>]</pre></td>
        <td><pre>LDR <em>Rd</em>, <em>label</em></pre></td>
    </tr>
    <tr>
        <td>Specify both registers for doubleword memory accesses. You must still obey rules about the register combinations you can use.</td>
        <td><pre>LDRD <em>Rd</em>, <em>addr_mode</em></pre></td>
        <td><pre>LDRD <em>Rd</em>, <em>Rd2</em>, <em>addr_mode</em></pre></td>
    </tr>
    <tr>
        <td><code>{<em>cond</em>}</code>, if used, is always the last element of all instructions.</td>
        <td><pre>ADD{<em>cond</em>}S&#13;LDR{<em>cond</em>}SB</pre></td>
        <td><pre>ADDS{<em>cond</em>}&#13;LDRSB{<em>cond</em>}</pre></td>
    </tr>
</table>

**Relaxation of requirements**

<table>
	<tr>
		<th align="left">Relaxation</th>
		<th align="left">Permitted syntax</th>
		<th align="left">Preferred syntax</th>
	</tr>
	<tr>
		<td>If the destination register is the same as the first operand, zou can use a two register form of the instruction.</td>
		<td><pre>ADD r1, r3</pre></td>
		<td><pre>ADD r1, r1, r3</pre></td>
	</tr>
</table>

**Differences between pre-UAL Thumb syntax aand UAL syntax**

<table>
    <tr>
        <th align="left">Change</th>
        <th align="left">Pre-UAL Thumb syntax</th>
        <th align="left">Preferred UAL syntax</th>
    </tr>
	<tr>
        <td>The default addressing mode for <code>LDM</code> and <code>STM</code> is <code>IA</code></td>
        <td><pre>LDMIA, STMIA</pre></td>
        <td><pre>LDM, STM</pre></td>
    </tr>
	<tr>
		<td>You must use the <code>S</code> postfix on instructions that update the flags. This change is essential to avoid conflict with 32-bit Thumb instructions.</td>
		<td><pre>ADD r1, r2, r3&#13;SUB r4, r5, #6&#13;MOV r0, #1&#13;LSR r1, r2, #1</pre></td>
		<td><pre>ADDS r1, r2, r3&#13;SUBS r4, r5, #6&#13;MOVS r0, #1&#13;LSRS r1, r2, #1</pre></td>
	</tr>
	<tr>
		<td>The preferred form for ALU instructions specifies three registers, even if the destination register is the same as the first operand. However, the UAL syntax allows the two register syntax.</td>
		<td><pre>ADD r7, r8&#13;SUB r1, #80</pre></td>
		<td><pre>ADD r7, r7, r8&#13;SUBS r1, r1, #80</pre></td>
	</tr>
	<tr>
		<td>If <code><em>Rd</em></code> and <code><em>Rn</em></code> are both Lo registers, <code>MOV <em>Rd</em>, <em>Rn</em></code> is disassembles as <code>ADDS <em>Rd</em>, <em>Rn</em>, #0</code>.</td>
		<td><pre>MOV r2, r3&#13;MOV r8, r9&#13;CPY r0, r1&#13;LSL r2, r3, #0</pre></td>
		<td><pre>ADDS r2, r3, #0&#13;MOV r8, r9&#13;MOV r0, r1&#13;MOVS r2, r3</pre></td>
	</tr>
	<tr>
		<td><code>NEG <em>Rd</em>, <em>Rn</em></code> is disassembled as <code>RSBS <em>Rd</em>, <em>Rn</em>, #0</code>.</td>
		<td><pre>NEG <em>Rd</em>, <em>Rn</em></pre></td>
		<td><pre>RSBS <em>Rd</em>, <em>Rn</em>, #0</pre></td>
	</tr>
	<tr>
		<td>When using the <code>LDR <em>Rd</em>, =<em>const</em></code> literal load pseudo-instruction, in pre-UAL syntax, the generated instruction might affect the condition code flags. In UAL syntax, the generated instruction sequence is guaranteed to not affect the condition code flags.</td>
		<td><pre>LDR r0, =0&#13;; generates the instruction:&#13;MOVS r0, #0</pre></td>
		<td><pre>LDR r0, =0&#13;; generates the instruction:&#13;LDR r0, [pc, #n]&#13;...&#13;DCD 0</pre></td>
</table>

**Optional hash with immediate constants**

You do not have to specify a hash before an immediate constant in any instruction syntax. <sup>armasm</sup>

You always have to specify a hash before an immediate constant in `divided` syntax. It is optional in `unified` syntax. <sup>GNU as</sup>
