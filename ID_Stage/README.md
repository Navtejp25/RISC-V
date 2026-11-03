# ID Stage (Instruction Decode)
// Internal wires
logic [4:0]  rs1, rs2, rd;
logic [6:0]  opcode;
logic [2:0]  fn3;
logic [11:0] imm;
logic [19:0] imm_uj;
logic        reg_write;
logic        reg_write_gated;

// Pass-through outputs from decoder
assign opcode_out_d = opcode;
assign fn3_out_d    = fn3;

// Gated register write enable: no register is written while stalled
assign reg_write_gated = reg_write && !stall;

// 1. Instruction Decoder
decoder u_decoder (
    .instruction (instruction),
    .rs1         (rs1),
    .rs2         (rs2),
    .rd          (rd),
    .opcode      (opcode),
    .imm11_5     (imm11_5),
    .fn3         (fn3),
    .imm         (imm),
    .imm_uj      (imm_uj),
    .fn7_5       (fn7_5)
);

// 2. Immediate Generator
imm_gen u_imm_gen (
    .imm         (imm),
    .imm_uj      (imm_uj),
    .opcode      (opcode),
    .imm_out     (imm_out)
);

// 3. Register File
register_file u_register_file (
    .clk         (clk),
    .reset       (reset),
    .reg_write   (reg_write_gated),
    .rs1_addr    (rs1),
    .rs2_addr    (rs2),
    .rd_addr     (rd),
    .rd_data     (wb_data),
    .rs1_data    (rs1_data),
    .rs2_data    (rs2_data)
);

// 4. Main Control
main_control u_main_control (
    .opcode      (opcode),
    .branch      (branch),
    .memtoreg    (memtoreg),
    .aluop       (aluop),
    .mem_write   (mem_write),
    .mem_read    (mem_read),
    .alu_src     (alu_src),
    .mux_inp     (mux_inp),
    .reg_write   (reg_write)
);
