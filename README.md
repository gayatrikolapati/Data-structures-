
4)emulate the UNIX ls –l command #include <stdio.h> #include <conio.h> #include <dos.h> #include <dir.h> #include <time.h> void display_file_info(struct ffblk file); void main() { struct ffblk file; int done; clrscr(); done = findfirst(".", &file, FA_ARCH | FA_RDONLY | FA_HIDDEN | FA_DIREC); printf("Attributes Size Date Time Name\n"); printf("------------------------------------------------------\n"); while (!done) { display_file_info(file); done = findnext(&file); } getch(); } void display_file_info(struct ffblk file) { struct tm *time_info; unsigned year, month, day, hour, minute, second; printf("%c", (file.ff_attrib & FA_RDONLY) ? 'r' : '-'); printf("%c", (file.ff_attrib & FA_HIDDEN) ? 'h' : '-'); printf("%c", (file.ff_attrib & FA_SYSTEM) ? 's' : '-'); printf("%c", (file.ff_attrib & FA_DIREC) ? 'd' : '-'); printf("%c", (file.ff_attrib & FA_ARCH) ? 'a' : '-'); printf(" "); printf("%8lu", file.ff_fsize); year = ((file.ff_fdate >> 9) & 0x7F) + 1980; month = (file.ff_fdate >> 5) & 0x0F; day = file.ff_fdate & 0x1F; hour = (file.ff_ftime >> 11) & 0x1F; minute = (file.ff_ftime >> 5) & 0x3F; second = (file.ff_ftime & 0x1F) * 2; printf(" %02d-%02d-%04d", day, month, year); printf(" %02d:%02d:%02d", hour, minute, second); printf(" %s\n", file.ff_name); }

5)how to execute two commands concurrently with a command pipe. Ex: - ls –l | sort #include<stdio.h> #include<stdlib.h> void main() { clrscr(); system("dir | sort"); //system("ls -l | sort"); getch(); }

6)Multiprogramming-Memory management-Implementation of fork (), wait (), exec() and exit (), System calls #include <stdio.h> #include <stdlib.h> #include <dos.h> void childProcess() { printf("Child process.\n"); delay(1000); } void parentProcess() { printf("Parent process.\n"); childProcess(); } void executeCommand(const char *command) { printf("Command: %s\n", command); system(command); } void main() { clrscr(); printf("Simulating fork(), wait(), exec(), exit() in DOS.\n"); parentProcess(); executeCommand("dir"); printf("Exiting program.\n"); exit(0); getch(); }

FCFS #include <stdio.h> int main() { int n, i, burst_time[50], waiting_time[50], turnaround_time[50]; int total_waiting_time = 0, total_turnaround_time = 0; printf("Enter the number of processes: "); scanf("%d", &n); printf("Enter the burst times of the processes:\n"); for (i = 0; i < n; i++) { printf("Process %d: ", i + 1); scanf("%d", &burst_time[i]); } waiting_time[0] = 0; for (i = 1; i < n; i++) { waiting_time[i] = burst_time[i - 1] + waiting_time[i - 1]; } for (i = 0; i < n; i++) { turnaround_time[i] = burst_time[i] + waiting_time[i]; } printf("\nProcess\tBurst Time\tWaiting Time\tTurnaround Time\n"); for (i = 0; i < n; i++) { total_waiting_time += waiting_time[i]; total_turnaround_time += turnaround_time[i]; printf("%d\t%d\t\t%d\t\t%d\n", i + 1, burst_time[i], waiting_time[i], turnaround_time[i]); } printf("\nAverage Waiting Time: %.2f", (float)total_waiting_time / n); printf("\nAverage Turnaround Time: %.2f", (float)total_turnaround_time / n); return 0; } 

SJF #include <stdio.h> int main() { int n, i, j, burst_time[50], waiting_time[50], turnaround_time[50], temp; int total_waiting_time = 0, total_turnaround_time = 0; printf("Enter the number of processes: "); scanf("%d", &n); printf("Enter the burst times of the processes:\n"); for (i = 0; i < n; i++) { printf("Process %d: ", i + 1); scanf("%d", &burst_time[i]); } for (i = 0; i < n-1; i++) { for (j = i+1; j < n; j++) { if (burst_time[i] > burst_time[j]) { temp = burst_time[i]; burst_time[i] = burst_time[j]; burst_time[j] = temp; } } } waiting_time[0] = 0; for (i = 1; i < n; i++) { waiting_time[i] = burst_time[i - 1] + waiting_time[i - 1]; } for (i = 0; i < n; i++) { turnaround_time[i] = burst_time[i] + waiting_time[i]; } printf("\nProcess\tBurst Time\tWaiting Time\tTurnaround Time\n"); for (i = 0; i < n; i++) { total_waiting_time += waiting_time[i]; total_turnaround_time += turnaround_time[i]; printf("%d\t%d\t\t%d\t\t%d\n", i + 1, burst_time[i], waiting_time[i], turnaround_time[i]); } printf("\nAverage Waiting Time: %.2f", (float)total_waiting_time / n); printf("\nAverage Turnaround Time: %.2f", (float)total_turnaround_time / n); return 0; } 

C)priority: #include <stdio.h> int main() { int n, i, j, burst_time[50], waiting_time[50], turnaround_time[50], priority[50], temp; int total_waiting_time = 0, total_turnaround_time = 0; printf("Enter the number of processes: "); scanf("%d", &n); printf("Enter the burst times and priorities of the processes:\n"); for (i = 0; i < n; i++) { printf("Process %d burst time: ", i + 1); scanf("%d", &burst_time[i]); printf("Process %d priority: ", i + 1); scanf("%d", &priority[i]); } for (i = 0; i < n-1; i++) { for (j = i+1; j < n; j++) { if (priority[i] > priority[j]) { temp = burst_time[i]; burst_time[i] = burst_time[j]; burst_time[j] = temp; temp = priority[i]; priority[i] = priority[j]; priority[j] = temp; } } } waiting_time[0] = 0; for (i = 1; i < n; i++) { waiting_time[i] = burst_time[i - 1] + waiting_time[i - 1]; } for (i = 0; i < n; i++) { turnaround_time[i] = burst_time[i] + waiting_time[i]; } printf("\nProcess\tBurst Time\tPriority\tWaiting Time\tTurnaround Time\n"); for (i = 0; i < n; i++) { total_waiting_time += waiting_time[i]; total_turnaround_time += turnaround_time[i]; printf("%d\t%d\t\t%d\t\t%d\t\t%d\n", i + 1, burst_time[i], priority[i], waiting_time[i], turnaround_time[i]); } printf("\nAverage Waiting Time: %.2f", (float)total_waiting_time / n); printf("\nAverage Turnaround Time: %.2f", (float)total_turnaround_time / n); return 0; } 

Round Robin) #include <stdio.h> int main() { int n, i, j, time_quantum, burst_time[50], remaining_time[50], waiting_time[50], turnaround_time[50]; int total_waiting_time = 0, total_turnaround_time = 0, time = 0, count = 0; printf("Enter the number of processes: "); scanf("%d", &n); printf("Enter the time quantum: "); scanf("%d", &time_quantum); printf("Enter the burst times of the processes:\n"); for (i = 0; i < n; i++) { printf("Process %d: ", i + 1); scanf("%d", &burst_time[i]); remaining_time[i] = burst_time[i]; } while (count < n) { for (i = 0; i < n; i++) { if (remaining_time[i] > 0) { if (remaining_time[i] > time_quantum) { remaining_time[i] -= time_quantum; time += time_quantum; } else { time += remaining_time[i]; waiting_time[i] = time - burst_time[i]; turnaround_time[i] = waiting_time[i] + burst_time[i]; total_waiting_time += waiting_time[i]; total_turnaround_time += turnaround_time[i]; remaining_time[i] = 0; count++; } } } } printf("\nProcess\tBurst Time\tWaiting Time\tTurnaround Time\n"); for (i = 0; i < n; i++) { printf("%d\t%d\t\t%d\t\t%d\n", i + 1, burst_time[i], waiting_time[i], turnaround_time[i]); } printf("\nAverage Waiting Time: %.2f", (float)total_waiting_time / n); printf("\nAverage Turnaround Time: %.2f", (float)total_turnaround_time / n); return 0; } 3a)MFT #include<stdio.h> #include<conio.h> void main() { int ms,bs,nob,ef,n,mp[10],tif=0; int i,p=0; clrscr(); printf("Enter the total memory available (in Bytes) -- "); scanf("%d",&ms); printf("Enter the block size (in Bytes) -- "); scanf("%d", &bs); nob=ms/bs; ef=ms - nob*bs; printf("\nEnter the number of processes -- "); scanf("%d",&n); for(i=0;i<n;i++) { printf("Enter memory required for process %d (in Bytes)-- ",i+1); scanf("%d",&mp[i]); } printf("\nNo. of Blocks available in memory -- %d",nob); printf("\n\nPROCESS\tMEMORY REQUIRED\t ALLOCATED\tINTERNAL FRAGMENTATION"); for(i=0;i<n && p<nob;i++) { printf("\n %d\t\t%d",i+1,mp[i]); if(mp[i] > bs) printf("\t\tNO\t\t---"); else { printf("\t\tYES\t%d",bs-mp[i]); tif = tif + bs-mp[i]; p++; } } if(i<n) printf("\nMemory is Full, Remaining Processes cannot be accomodated"); printf("\n\nTotal Internal Fragmentation is %d",tif); printf("\nTotal External Fragmentation is %d",ef); getch(); }

3a)MFT
#include<stdio.h> 
#include<conio.h> 
void main()
{
int ms,bs,nob,ef,n,mp[10],tif=0;
int i,p=0;
clrscr();
printf("Enter the total memory available (in Bytes) -- ");
scanf("%d",&ms);
printf("Enter the block size (in Bytes) -- ");
scanf("%d", &bs);
nob=ms/bs; ef=ms - nob*bs;
printf("\nEnter the number of processes -- ");
scanf("%d",&n);
for(i=0;i<n;i++)
{
printf("Enter memory required for process %d (in Bytes)-- ",i+1);
scanf("%d",&mp[i]);
}
printf("\nNo. of Blocks available in memory -- %d",nob);
printf("\n\nPROCESS\tMEMORY REQUIRED\t ALLOCATED\tINTERNAL FRAGMENTATION");
for(i=0;i<n && p<nob;i++)
{
printf("\n %d\t\t%d",i+1,mp[i]);
if(mp[i] > bs)
printf("\t\tNO\t\t---");
else
{
printf("\t\tYES\t%d",bs-mp[i]);
tif = tif + bs-mp[i];
p++;
}
}
if(i<n)
printf("\nMemory is Full, Remaining Processes cannot be accomodated");
printf("\n\nTotal Internal Fragmentation is %d",tif);
printf("\nTotal External Fragmentation is %d",ef);
getch();
}

3b)MVT #include<stdio.h> #include<conio.h> void main() { int ms,mp[10],i, temp,n=0; char ch = 'y'; clrscr(); printf("\nEnter the total memory available (in Bytes)-- "); scanf("%d",&ms); temp=ms; for(i=0;ch=='y';i++,n++) { printf("\nEnter memory required for process %d (in Bytes) -- ",i+1); scanf("%d",&mp[i]); if(mp[i]<=temp) { printf("\nMemory is allocated for Process %d ",i+1); temp = temp - mp[i]; } else { printf("\nMemory is Full"); break; } printf("\nDo you want to continue(y/n) -- "); scanf(" %c", &ch); } printf("\n\nTotal Memory Available -- %d", ms); printf("\n\n\tPROCESS\t\t MEMORY ALLOCATED "); for(i=0;i<n;i++) printf("\n \t%d\t\t%d",i+1,mp[i]); printf("\n\nTotal Memory Allocated is %d",ms-temp); printf("\nTotal External Fragmentation is %d",temp); getch(); }

4)implement first fit, best fit and worst fit algorithm for memory management. 
#include <stdio.h> #define BLOCKS 5 #define PROCESSES 3 void allocateMemory(int blocks[], int m, int processes[], int n, int choice) { int i, j, idx; for (i = 0; i < n; i++) { idx = -1; for (j = 0; j < m; j++) { if (blocks[j] >= processes[i] && (choice == 1 || (choice == 2 && (idx == -1 || blocks[j] < blocks[idx])) || (choice == 3 && (idx == -1 || blocks[j] > blocks[idx])))) { idx = j; } } if (idx != -1) { printf("P%d allocated to B%d\n", i + 1, idx + 1); blocks[idx] -= processes[i]; } else { printf("P%d cannot be allocated\n", i + 1); } } } void main() { int blocks[BLOCKS], processes[PROCESSES], i, blocks1[BLOCKS], blocks2[BLOCKS], blocks3[BLOCKS]; clrscr(); printf("Enter the sizes of %d blocks:\n", BLOCKS); for (i = 0; i < BLOCKS; i++) { printf("Block %d: ", i + 1); scanf("%d", &blocks[i]); } printf("Enter the sizes of %d processes:\n", PROCESSES); for (i = 0; i < PROCESSES; i++) { printf("Process %d: ", i + 1); scanf("%d", &processes[i]); } for (i = 0; i < BLOCKS; i++) { blocks1[i] = blocks[i]; blocks2[i] = blocks[i]; blocks3[i] = blocks[i]; } printf("\nFirst Fit:\n"); allocateMemory(blocks1, BLOCKS, processes, PROCESSES, 1); printf("\nBest Fit:\n"); allocateMemory(blocks2, BLOCKS, processes, PROCESSES, 2); printf("\nWorst Fit:\n"); allocateMemory(blocks3, BLOCKS, processes, PROCESSES, 3); getch(); } 

5 Dead lock Avoidance #include <stdio.h> #include <stdbool.h>
#define MAX 10 #define RESOURCES 3 #define PROCESSES 5
int available[RESOURCES]; int max[PROCESSES][RESOURCES]; int allocation[PROCESSES][RESOURCES]; int need[PROCESSES][RESOURCES];
bool isSafeState() { int work[RESOURCES], finish[PROCESSES] = {0}; for (int i = 0; i < RESOURCES; i++) work[i] = available[i]; int count = 0; while (count < PROCESSES) { bool found = false; for (int i = 0; i < PROCESSES; i++) { if (finish[i] == 0) { bool canAllocate = true; for (int j = 0; j < RESOURCES; j++) { if (need[i][j] > work[j]) { canAllocate = false; break; } } if (canAllocate) { for (int j = 0; j < RESOURCES; j++) work[j] += allocation[i][j]; finish[i] = 1; count++; found = true; } } } if (!found) return false; } return true; }
int main() { int i, j; printf("Enter the number of processes: "); int n; scanf("%d", &n); printf("Enter the number of resources: "); int m; scanf("%d", &m); printf("Enter the available resources: "); for (i = 0; i < m; i++) scanf("%d", &available[i]); printf("Enter the maximum resources for each process:\n"); for (i = 0; i < n; i++) for (j = 0; j < m; j++) scanf("%d", &max[i][j]); printf("Enter the allocated resources for each process:\n"); for (i = 0; i < n; i++) for (j = 0; j < m; j++) scanf("%d", &allocation[i][j]); for (i = 0; i < n; i++) for (j = 0; j < m; j++) need[i][j] = max[i][j] - allocation[i][j]; if (isSafeState()) printf("System is in a safe state.\n"); else printf("System is not in a safe state.\n"); return 0; } 

6 )dead lock prevention : #include <stdio.h> #include <stdbool.h>
#define MAX 10 #define RESOURCES 3 #define PROCESSES 5
int available[RESOURCES]; int max[PROCESSES][RESOURCES]; int allocation[PROCESSES][RESOURCES]; int need[PROCESSES][RESOURCES];
bool isSafeState() { int work[RESOURCES], finish[PROCESSES] = {0}; for (int i = 0; i < RESOURCES; i++) work[i] = available[i]; int count = 0; while (count < PROCESSES) { bool found = false; for (int i = 0; i < PROCESSES; i++) { if (finish[i] == 0) { bool canAllocate = true; for (int j = 0; j < RESOURCES; j++) { if (need[i][j] > work[j]) { canAllocate = false; break; } } if (canAllocate) { for (int j = 0; j < RESOURCES; j++) work[j] += allocation[i][j]; finish[i] = 1; count++; found = true; } } } if (!found) return false; } return true; }

int main() { int i, j; printf("Enter the number of processes: "); int n; scanf("%d", &n); printf("Enter the number of resources: "); int m; scanf("%d", &m); printf("Enter the available resources: "); for (i = 0; i < m; i++) scanf("%d", &available[i]); printf("Enter the maximum resources for each process:\n"); for (i = 0; i < n; i++) for (j = 0; j < m; j++) scanf("%d", &max[i][j]); printf("Enter the allocated resources for each process:\n"); for (i = 0; i < n; i++) for (j = 0; j < m; j++) scanf("%d", &allocation[i][j]); for (i = 0; i < n; i++) for (j = 0; j < m; j++) need[i][j] = max[i][j] - allocation[i][j];

if (isSafeState()) printf("System is in a safe state.\n");
else printf("System is not in a safe state.\n");
return 0;
} 

7a)FIFO #include <stdio.h> #include <stdlib.h> #define MAX_FRAMES 3 void fifoPageReplacement(int pages[], int n, int capacity) { int frames[MAX_FRAMES]; int pageFaults = 0; int i, j, front = 0; for ( i = 0; i < capacity; i++) { frames[i] = -1; } for ( i = 0; i < n; i++) { int currentPage = pages[i]; int pageFound = 0; for ( j = 0; j < capacity; j++) { if (frames[j] == currentPage) { pageFound = 1; break; }} if (!pageFound) { frames[front] = currentPage; front = (front + 1) % capacity; pageFaults++; } printf("Page %d loaded into memory.\n", currentPage); } printf("\nTotal page faults: %d\n", pageFaults); } void main() { int pages[] = {7, 0, 1, 2, 0, 3, 0, 4, 2, 3, 0, 3, 0}; int n = sizeof(pages) / sizeof(pages[0]); int capacity = 3; clrscr(); fifoPageReplacement(pages, n, capacity); getch(); }

7b)LRU #include <stdio.h> #define MAX_FRAMES 10 void simulateLRU(int pages[], int numPages, int numFrames) { int frames[MAX_FRAMES] = {-1}; int pageFaults = 0, i, j, found, lruIndex; for (i = 0; i < numPages; i++) { found = 0; for (j = 0; j < numFrames && !found; j++) { if (frames[j] == pages[i]) found = 1; } if (!found) { pageFaults++; for (j = 0; j < numFrames && frames[j] != -1; j++); if (j < numFrames) frames[j] = pages[i]; else { lruIndex = 0; for (j = 1; j < numFrames; j++) { if (frames[j] != pages[i]) lruIndex = j; } frames[lruIndex] = pages[i]; }} printf("Frames: "); for (j = 0; j < numFrames; j++) { if (frames[j] != -1) printf("%d ", frames[j]); } printf("\n" ); } printf("Page faults: %d\n", pageFaults); } void main() { int pages[] = {7, 0, 1, 2, 0, 3, 0, 4, 2, 3, 0, 3, 2}; int numPages = sizeof(pages) / sizeof(pages[0]); clrscr(); simulateLRU(pages, numPages, 3); getch(); }

7c)LFU #include <stdio.h> #include <conio.h> typedef struct { int page, freq, lastUsed; } Page; int findLFU(Page f[], int n) { int i, idx = 0; for (i = 1; i < n; i++) { if (f[i].freq < f[idx].freq || (f[i].freq == f[idx].freq && f[i].lastUsed < f[idx].lastUsed)) { idx = i; }} return idx; } int lfu(int pages[], int n, int size) { Page f[26], temp; int i, j, count = 0, faults = 0; for (i = 0; i < n; i++) { int p = pages[i], found = 0; for (j = 0; j < count; j++) { if (f[j].page == p) { f[j].freq++; f[j].lastUsed = i; found = 1; break; }} if (!found) { faults++; if (count < size) { temp.page = p; temp.freq = 1; temp.lastUsed = i; f[count++] = temp; } else { int idx = findLFU(f, size); temp.page = p; temp.freq = 1; temp.lastUsed = i; f[idx] = temp; }}} return faults; } void main() { int pages[] = {1, 2, 3, 2, 1, 4, 2, 3}; clrscr(); printf("Page Faults: %d\n", lfu(pages, sizeof(pages) / sizeof(pages[0]), 3)); getch(); }

8a)Sequenced #include<stdio.h> int main() { int f[50], i, st, j, len, c, k; clrscr(); for(i = 0; i < 50; i++) f[i] = 0; X: printf("Enter starting block & length of file: "); scanf("%d%d", &st, &len); for(j = st; j < (st + len); j++) if(f[j] == 0) { f[j] = 1; printf("%d->%d\n", j, f[j]); } else { printf("Block already allocated"); break; } if(j == (st + len)) printf("File is allocated to disk"); printf("\nWant to enter more files? (y-1/n-0): "); scanf("%d", &c); if(c == 1) goto X; else return 0; }

8b)Indexed #include<stdio.h> int f[50],i,k,j,inde[50],n,c,count=0,p; int main() { clrscr(); for(i=0;i<50;i++) f[i]=0; x: printf("Enter index block: "); scanf("%d",&p); if(f[p]==0) { f[p]=1; printf("Enter no. of files on index: "); scanf("%d",&n); } else { printf("Block already allocated\n"); goto x; } for(i=0;i<n;i++) scanf("%d",&inde[i]); for(i=0;i<n;i++) if(f[inde[i]]==1) { printf("Block already allocated"); goto x; } for(j=0;j<n;j++) f[inde[j]]=1; printf("Allocated"); printf("\nFile indexed"); for(k=0;k<n;k++) printf("\n%d->%d:%d",p,inde[k],f[inde[k]]); printf("\nEnter 1 to enter more files or 0 to exit: "); scanf("%d",&c); if(c==1) goto x; else return 0; }

8c)Linked #include<stdio.h> int main() { int f[50],p,i,j,k,a,st,len,n,c; clrscr(); for(i=0;i<50;i++) f[i]=0; printf("How many blocks are already allocated? : "); scanf("%d",&p); printf("Enter block no's that are already allocated: "); for(i=0;i<p;i++) { scanf("%d",&a); f[a]=1; } X: printf("Enter starting index block & length: "); scanf("%d%d",&st,&len); k=len; for(j=st;j<(k+st);j++) { if(f[j]==0) { f[j]=1; printf("%d->%d\n",j,f[j]); } else { printf("%d->File is already allocated\n",j); k++; } } printf("Want to enter one more file? (yes-1/no-0): "); scanf("%d",&c); if(c==1) goto X; else return 0; }
