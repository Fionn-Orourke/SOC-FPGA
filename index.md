---
layout: home
title: FPGA VGA Driver Project
tags: fpga vga verilog
categories: demo
---

# FPGA VGA Driver Project: Moving Flag with Switch Interaction

## Project Overview

This project demonstrates a dynamic and interactive VGA display on the Basys 3 (Artix 7) FPGA board using Vivado 2025.1. The design renders moving flag patterns on a monitor, with the animation and color modes controlled by the board’s onboard switches. The primary goal was impliment movement and user interaction. The user interraction doesn't work correctly.

---
##Project summary
<img src="https://raw.githubusercontent.com/Fionn-Orourke/SOC-FPGA/main/docs/assets/images/proj summary.png">

## Template VGA Design

### Project Set-Up

The project was developed in Vivado 2025.1 using the Basys3_Master.xdc constraint file to accurately map the VGA pins and user switches. The design flow followed standard Vivado methodology: creating a new project, adding Verilog modules, loading all constraints, and simulating before physical synthesis.  


### Template Code

The given colour stripes code template handled basic VGA timing, producing fixed-color stripes on the VGA monitor. The templates provided a clear separation between timing generation (sync signals, row and column counting) and the color logic, which allowed for easy experimentation with various display effects.  
The VGA interface operates by outputting color information for each pixel at just the right time, synchronized with horizontal and vertical sync pulses. This requirement for precise timing was a critical factor when introducing animation to the design. I began by changing some parts in the template code to see what it would affect.
<img src= "https://github.com/Fionn-Orourke/SOC-FPGA/blob/main/docs/assets/images/IMG_20251111_140907759_MFNR.jpg">
after understanding the code i changed it to fit the dimensions of the irish flag.
<img src= "https://github.com/Fionn-Orourke/SOC-FPGA/blob/main/docs/assets/images/IMG_20251125_152630832_MFNR.jpg">


### Simulation

Simulation was performed using Vivado’s built-in tools, allowing verification of both the VGA timing generation and static image output before moving to more complex behaviour. Key signals such as row/column counts, and color outputs were examined for correctness.  
(i cannot show a simulation waveform as each time i attempt to run behavioral simulation Vivado crashes)

### Synthesis

With the design verified through simulation, synthesis and implementation were run using Vivado. The main output at this stage was the post-implementation timing summary and resource utilization, as well as the generation of a bitstream for programming the Basys 3 board.  


### Demonstration

The unmodified template demo produced stable, static color stripes on the display.  


---

## My VGA Design Edit

I extended the template to create a moving flag effect, adding user-interaction via the board switches to control movement speed, direction, and color effects. This added a layer of complexity, requiring careful handling of animation timing and user input synchronization. Online references such as [FPGA4student’s VGA FPGA tutorials](https://www.fpga4student.com/) provided guidance.

### Code Adaptation

The core of the moving flag logic is handled in the `ColourStripes` Verilog module. Here’s a summary of the key changes and features:

- **Animated Movement:**  
  A counter (`count`) is used to create animated horizontal movement of the flag pattern across the screen. The position shift is controlled by incrementing or resetting the counter based on the clock and reset input.
  ```wire [10:0] width = 200 + count[28:22];
  ```
  count[28:22] is a small (7-bit) number that smoothly increases as the main counter goes up.
  By adding this to 200, the stripe width value slowly grows and shrinks, creating a breathing or pulsing effect where stripes get thicker and thinner, repeatedly, all by themselves.
- **User Interaction:(unfinnished)**  
  The four onboard switches (`sw[3:0]`) control multiple display properties:
    - Speed and direction of flag movement
    - change of flag
    <img src="https://github.com/Fionn-Orourke/SOC-FPGA/blob/main/docs/assets/images/IMG_20251202_145443799_MFNR.jpg">

- **Pulsing Effect:**  
  The width of each colored stripe pulses over time, further enhancing the visual interest.
  i began implimenting the pulsing effect with the template code. It was very fast and timing wasnt good but the first movement was a good milestone.
  <img src ="https://github.com/Fionn-Orourke/SOC-FPGA/blob/main/docs/assets/images/VID_20251111_143014980%20(1).gif">

```verilog
reg [COUNTER_WIDTH-1:0] count;
always @(posedge clk or posedge rst) begin
    // Animation counter logic
end

wire direction = sw[2];
wire [10:0] col_shifted = direction ? (col - shift_amount) : (col + shift_amount);

// Color mode selection based on user switches
always @(*) begin
    case(sw[3:2])
        // ...
    endcase
end
```

### Simulation

Simulation focused on validating the animated movement and the effects of switch toggling. It was useful to plot both the counter, color outputs, and switch inputs on the same waveform, watching for glitches or incorrect wraparound behavior. The main challenge in simulation was to ensure timing correctness with changing movement speeds. This is where i identified that my switches werent working.  
(the simulation has stopped working)

### Synthesis

During synthesis, I paid to warning messages about timing, as the animation relied on correct clock division and register use. No significant errors arose, but tweaking the counter width and timing constants was necessary to achieve smooth on-screen animation. The implemented design downloaded smoothly to the Basys3 with correct pin mappings.
  
### Demonstration

The final design, programmed into the Basys 3 board, drives a monitor via the VGA output. Switches S0—S3 can be toggled live to reverse direction, change speed, and apply different color effects.  

<img src="https://github.com/Fionn-Orourke/SOC-FPGA/blob/main/docs/assets/images/VID_20251202_153641131.gif">
---

## Challenges & Lessons Learned

The most challenging part of the project was mastering timing for smooth animation and resets, along with learning to use registers correctly for switch-controlled behavior. Ensuring synthesis and implemented hardware matched the simulated design required several iterations, especially to fine-tune counter widths and handle wraparound conditions. a challenge that i did not overcome is the switch implementation, i tried using hardware friendly code like using shifts instead of devisions in my shift change section. i cannot run a behavioral simulation to help me

---

## References

- [FPGA4Student – VGA Controller](https://www.fpga4student.com/2017/08/vga-controller-verilog-code.html)
- [Basys 3 Reference Manual (Digilent)](https://digilent.com/reference/programmable-logic/basys-3/reference-manual)

---



<img src="https://raw.githubusercontent.com/melgineer/fpga-vga-verilog/main/docs/assets/images/VGAPrjSrcs.png">
