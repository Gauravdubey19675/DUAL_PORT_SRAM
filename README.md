module SRAM(data_out_1 , data_out_2 ,error, data_in_1 , data_in_2 , rd_en_2 , rd_en_1 , wr_en_2 , wr_en_1 ,address_1 , address_2  , clk , reset);
input clk;
input reset;
input rd_en_2;
input rd_en_1;
input wr_en_2;
input wr_en_1;
input [7:0]data_in_1; 
input [7:0]data_in_2;
input [9:0]address_1;
input [9:0]address_2;
output reg[7:0]data_out_2;
output reg[7:0]data_out_1;
output error;


reg [7:0]mem[0:1023];

always @(negedge clk or posedge reset)
begin
if(reset)
begin
  data_out_1<=8'b0;
  data_out_2<=8'b0; 
end
else if(!reset)
begin
if (wr_en_1 && rd_en_2 && !wr_en_2 && !rd_en_1)
begin
 if (address_1  == address_2)
begin
data_out_2<=data_in_1;
mem[address_1]<=data_in_1;
end
else
begin
mem[address_1]<=data_in_1;
data_out_2<=mem[address_2];
end
end

else if(wr_en_2 && rd_en_1 && !wr_en_1 && !rd_en_2)
begin
if (address_1 == address_2)
begin
data_out_1<=data_in_2;
mem[address_2]<=data_in_2;
end
else
begin
mem[address_2]<=data_in_2;
data_out_1<=mem[address_1];
end
end
  
else if (wr_en_1 &&  wr_en_2  && !rd_en_1 && !rd_en_2)
begin
if (address_1 != address_2)
begin
mem[address_1]<=data_in_1;
mem[address_2]<=data_in_2;
end
end

else if (rd_en_1 && rd_en_2  && !wr_en_1  && !wr_en_2)
begin
data_out_1 <= mem[address_1];
data_out_2 <= mem[address_2]; 
end

end
end
    
assign error =(wr_en_2 && wr_en_1 && (address_1 == address_2)) | ((wr_en_1 && rd_en_1) | (wr_en_2 && rd_en_2)) ;
  
endmodule







