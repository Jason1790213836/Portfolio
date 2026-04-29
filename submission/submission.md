## 1. A cool thing I built

I built a streaming Sobel edge detection accelerator on FPGA (PYNQ-Z2) using Vitis HLS. Instead of buffering the whole image, I designed a fully streaming pipeline with line buffers and a sliding window so that the system processes one pixel per cycle continuously.

What makes it interesting is that the design behaves more like a hardware dataflow engine than a traditional program — data moves through the system like a fluid rather than being randomly accessed. I optimized the datapath from 32-bit to 12-bit and removed multipliers, pushing the design close to timing limits while still sustaining ~100M+ pixels/sec.

It was originally a course project of NYU Prof Sundeep Applied Hardware Design Course, really interesting, but I went significantly beyond requirements by building a full DMA + AXI-Stream pipeline and deploying it on real hardware. 

![Result](../images/sobel.png)

------

## 2. My setup

I mainly use VS Code on Windows/Linux with a mix of CLI tools depending on the domain. For embedded and firmware work, I often rely on PlatformIO and STM32Cube environments, while for FPGA development I use Vivado/Vitis and interact with the system through Python (PYNQ).

I like setups that let me quickly move between abstraction levels — from C/C++ to hardware or registers — without being locked into a heavy IDE workflow. Over time I’ve moved away from “all-in-one IDEs” toward more modular setups where I can script, automate, and debug faster.

Also, I tend to build small tools/scripts around my workflow (Python, Bash) to automate repetitive tasks, especially for testing or data logging.

------

## 3. Debugging a randomly resetting PCB

First assumption: this is almost always power or timing until proven otherwise.

My first steps:

- Monitor power rails with an oscilloscope (look for dips, noise, or transient spikes)
- Check reset line behavior (is it externally triggered or internal watchdog?)
- Add logging (UART / GPIO toggle) to narrow down where it resets
- Disable non-essential peripherals to isolate the system

If nothing obvious shows up, I start thinking about:

- Brownout detection / regulator instability
- EMI / signal integrity issues
- Firmware bugs like stack overflow or race conditions

In a real lab project, I debugged a system with intermittent failures that turned out to be a power distribution issue combined with incorrect chip selection logic. The system would occasionally enter invalid states due to unstable switching — fixing both hardware routing and control logic solved it.

I change strategy when measurements stop giving new information — then I reduce the system to minimal components and rebuild complexity gradually.

------

## 4. What firmware engineers do wrong

A common mistake is treating firmware like “just software.”

Firmware lives in a physical system:

- Timing matters
- Power matters
- Hardware states persist

Another issue is over-reliance on abstraction layers. While HALs and RTOS are useful, many engineers stop understanding what’s happening underneath, which makes debugging much harder when something goes wrong. People should dive more deep into the system-level thinking instead of facial problem analysis.

I also think people underestimate dataflow thinking. Many problems (like sensor pipelines or DSP) are much easier to reason about as streaming systems rather than stateful programs.



------

## 5. A sketchy firmware fix I’m secretly proud of

Yes — one that I’m honestly a little proud of.

I was debugging a PCB-based sensing system where the amplifier output would randomly jump to around ~5V, even though the expected signal was near ground. After probing the circuit, I realized the input node was effectively floating due to a missing pull-down path, which made it extremely sensitive to parasitic coupling.

As a quick fix, I soldered a parallel pull-down resistor directly onto the board to stabilize the node. This immediately reduced the parasitic voltage from ~5V down to ~0.1V, allowing the system to operate reliably enough for testing. It was definitely a hack, but it kept the project alive while we investigated the root cause.

Later, I redesigned the layout using a low-noise return path strategy and identified that the real issue was a split ground plane causing unstable reference levels. After fixing the grounding and layout, the noise was reduced further to ~0.01mV and the system became fully stable.

------

# (Optional Portfolio Link Section)

GitHub / Projects:
 https://github.com/Jason1790213836/Portfolio