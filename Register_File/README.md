# Register File

// 32-bit width, 32-entry register array
logic [31:0] rf [31:0];

// Combinational reads: x0 is hardwired to 0
assign rs1_data = (rs1_sel == 5'b0) ? 32'b0 : rf[rs1_sel];
assign rs2_data = (rs2_sel == 5'b0) ? 32'b0 : rf[rs2_sel];

// Synchronous write with asynchronous active-high reset
always_ff @(posedge clk or posedge reset) begin
    if (reset) begin
        integer i;
        for (i = 0; i < 32; i = i + 1) begin
            rf[i] <= 32'b0;
        end
    end else if (reg_write && (rd_sel != 5'b0)) begin
        rf[rd_sel] <= wb_data;
    end
end
