1 - Porting FlameGraph to rust


When running an application in Linux, you can use these commands before your application to understasnd more about where the CPU actually spending time when running your applicaation:
 
- ***perf stat***
- ***perf record -g***
    - After you are done with command, you can run *perf report* which reads the report for you! However, it is not much usefull!
- ***perf record -g --call-graph dwarf***



What is better for this?
Use FlameGraph! :)



On MAC you can use dTrace instead of perf:

First run you application when running dTrace:

```bash
sudo dtrace -n 'profile-997 /pid == $target/ { @[ustack()] = count(); }' -c "./target/release/your_app" -o out.stacks
```


Then you need to run flamegraph:

```bash
./Flamegraph/stackcollapse.pl out.stacks > out.folded
./Flamegraph/flamegraph.pl out.folded > flamegraph.svg
```



**Hardware and Softwares :D :**




look at Firefox multi-account container
look at [j](https://github.com/wting/autojump) command 
