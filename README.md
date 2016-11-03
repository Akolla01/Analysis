# Analysis
library(xlsx)
library(openxlsx)
library(zoo)
args=commandArgs(TRUE)
input_data=read.xlsx("Ril Last 3years sales.xlsx", sheet=1)
YearSet=subset(input_data, as.numeric(format(as.Date(input_data$Calendar.Day,origin = "1899-12-30"), "%Y"))==args[1])
write.xlsx(YearSet,"Year_data.xlsx")
#wb=loadWorkbook ("/home/anjali/anjali/INFERNEON/Monthly_Sales1.xlsx", create = TRUE)
for(i in 1:12)
{
monthset=subset(YearSet, as.numeric(format(as.Date(YearSet$Calendar.Day,origin = "1899-12-30"), "%m"))==i)
month_name=month.abb[i]
write.table(monthset, file = paste(month_name,"xlsx",sep="."),sep=";",row.names=FALSE)
#data=read.table(file = paste(month_name,"xlsx",sep="."),sep=";",header=FALSE)
EAN_data=table(monthset$EAN)
df=as.data.frame(EAN_data)
#df=as.data.frame(table(monthset$EAN))
names(df)=c("EAN","times")
df=df[order(df$times,decreasing = T),]
top_EAN=df[1:5,]
write.table(top_EAN,file=paste(month_name,"_Top_EAN.xlsx",sep=""),sep=";",row.names=FALSE)
data=read.table(file = paste(month_name,"_Top_EAN.xlsx",sep=""),sep=";",header=FALSE)
write.table (data, file = "top_EANdata.xlsx", sep = ";",col.names =FALSE, append = TRUE, dec =",",row.names = FALSE)
}
data=read.table(file="top_EANdata.xlsx",sep = ";",stringsAsFactors=FALSE)
dataa=data[data$V1!="EAN",]
datab=as.numeric(dataa[,2])
mvector=matrix(datab, nrow=5,ncol=12, byrow=FALSE)
m=month.abb[1:12]
png(file=paste("Monthly sales of",args[1], "year.png",sep=""),width=1500,height=900)
xx=barplot(mvector,main = paste("Monthly Sales of",args[1],"Year",sep=""),names.arg=m,col=rainbow(5), beside=TRUE,xlab="Month",ylab="Frequency",xlim=c(0,70), legend = c("Top1","Top2","Top3","Top4","Top5"),args.legend = list( x = "topleft", cex = 1.5),cex.axis=2, cex.names=2,col.lab="red", cex.lab=2.5,cex.main = 3,col.main="green")
#text(x = xx, y =mvector ,label=paste(dataa$V1,mvector,sep="="), pos = 4,cex = 1, col = "dark blue")
text(x = xx, y =mvector ,label=dataa$V1, pos = 4,cex = 1, col = "dark blue")
dev.off()
