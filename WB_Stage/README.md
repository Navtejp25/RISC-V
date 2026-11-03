# WB Stage (Write Back)

always_comb begin
    unique case (memtoreg)
        2'b00: begin
            wb_data = alu_out;
        end
        2'b01: begin
            wb_data = mem_out;
        end
        2'b10: begin
            case (opcode_out_d)
                7'b1101111, // JAL
                7'b1100111: // JALR
                    wb_data = return_addr;
                7'b0110111: // LUI
                    wb_data = imm_out;
                7'b0010111: // AUIPC
                    wb_data = pc_signed_offset;
                default:
                    wb_data = alu_out;
            endcase
        end
        2'b11: begin
            wb_data = alu_out;
        end
        default: begin
            wb_data = alu_out;
        end
    endcase
end
