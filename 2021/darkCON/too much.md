The challenge is an "angrable" challenge, cause the input is check with simple mathematic operations. We can use these 2 piece of cose as an oracle for desired address and wrong address:
```c
puts("Congrats!!! You have cracked my code.");

puts("Please try harder!!!!")
```


# EXPLOIT
```python
import angr, claripy
target = angr.Project('rev', auto_load_libs=False)
input_len = 200
inp = [claripy.BVS('flag_%d' %i, 8) for i in range(input_len)]
flag = claripy.Concat(*inp + [claripy.BVV(b'\n')])


desired = 0x46d6
wrong = 0x46e4

st = target.factory.full_init_state(args=["./rev"], stdin=flag)
for k in inp:
    st.solver.add(k < 0x7f)
    st.solver.add(k > 0x20)


sm = target.factory.simulation_manager(st)
sm.run()
y = []
for x in sm.deadended:
    if b"Congrats!!! You have cracked my code." in x.posix.dumps(1):
        y.append(x)

#grab the first ouptut
valid = y[0].posix.dumps(0)
print(valid)
```
# FLAG
`darkCON{4r3_y0u_r34lly_th1nk1n9_th4t_y0u_c4n_try_th15_m4nu4lly???_Ok_I_th1nk_y0u_b3tt3r_us3_s0m3_aut0m4t3d_t00ls_l1k3_4n9r_0r_Z3_t0_m4k3_y0ur_l1f3_much_e4s13r.C0ngr4ts_f0r_s0lv1in9_th3_e4sy_ch4ll3ng3}`
                                                  
