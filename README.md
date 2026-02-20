# Traffic-Light-Controller-RTL-to-GDSII-VLSI-Design-Flow
In this Project i have created a Traffic Light Controller using Verilog Code with the help of testbench to simulate with the waveform anaylsis followed by creating a gatelevel netlist of it then physical design steps such as routing floorplanning and placement on 90nm technology.
## Tools Used:
Cadence Simulator (Waveform generation)
Cadence Genus ( Generating a gatelevel netlist)
Cadence Innovus ( Physical Design )
Cadence Virtuoso ( Viewing of GDS File)
Verilog HDL (Language used for RTL implementation)
## Working and Verilog Code
This traffic light controller manages an intersection with two directions: North–South (NS) and East–West (EW). To avoid collisions, only one direction is allowed to move at a time.

Operation

The controller works on a fixed-time sequence driven by a clock.

North–South and East–West signals are never green simultaneously.

## verilog code
module tlc (
    input clk,
    input reset,
    output reg [2:0] light_NS, // [Red, Yellow, Green]
    output reg [2:0] light_EW  // [Red, Yellow, Green]
);

    parameter NS_GREEN  = 2'b00,
              NS_YELLOW = 2'b01,
              EW_GREEN  = 2'b10,
              EW_YELLOW = 2'b11;

    reg [1:0] state, next_state;
    reg [3:0] count; // Timer for light duration

    // State Transition Logic
    always @(posedge clk or posedge reset) begin
        if (reset) begin
            state <= NS_GREEN;
            count <= 0;
        end else begin
            if (count == 4'd10) begin // Change state after 10 clock cycles
                state <= next_state;
                count <= 0;
            end else begin
                count <= count + 1;
            end
        end
    end

    // Next State Logic
    always @(*) begin
        case (state)
            NS_GREEN:  next_state = NS_YELLOW;
            NS_YELLOW: next_state = EW_GREEN;
            EW_GREEN:  next_state = EW_YELLOW;
            EW_YELLOW: next_state = NS_GREEN;
            default:   next_state = NS_GREEN;
        endcase
    end

    // Output Logic (Red-Yellow-Green encoding)
    always @(*) begin
        case (state)
            NS_GREEN:  begin light_NS = 3'b001; light_EW = 3'b100; end
            NS_YELLOW: begin light
            _NS = 3'b010; light_EW = 3'b100; end
            EW_GREEN:  begin light_NS = 3'b100; light_EW = 3'b001; end
            EW_YELLOW: begin light_NS = 3'b100; light_EW = 3'b010; end
            default:   begin light_NS = 3'b100; light_EW = 3'b100; end
        endcase
    end
endmodule
## Verilog Testbench
module tlc_tb;

    // Inputs
    reg clk;
    reg reset;

    // Outputs
    wire [2:0] light_NS;
    wire [2:0] light_EW;

    // Instantiate the Unit Under Test (UUT)
    tlc uut (
        .clk(clk), 
        .reset(reset), 
        .light_NS(light_NS), 
        .light_EW(light_EW)
    );

    // Clock generation (10ns period = 100MHz)
    always #5 clk = ~clk;

    initial begin
        // Initialize Inputs
        clk = 0;
        reset = 1;

        // Wait 20ns for global reset to settle
        #20;
        reset = 0;
        
        // Let the simulation run long enough to see several cycles
        // Since each state takes 10 cycles, we need at least 400ns to see a full loop
        #500;

        $display("Simulation complete.");
        $finish;
    end
      
    // Monitor the changes in the console
    initial begin
        $monitor("Time=%0t | Reset=%b | NS=%b | EW=%b", $time, reset, light_NS, light_EW);
    end

endmodule

## Cadence SDC File
// Create clock on clk port
// Clock period = 10 ns (100 MHz)
// Rising edge at 0 ns, falling edge at 5 ns
create_clock -name clk -period 10.0 -waveform {0 5} [get_ports clk]

## Author
Shivansh Mohan ,
B.Tech Electronics and Communication Engineering



