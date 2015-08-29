# code

function mypatched = myPatchBasedFiltering(file,sigma)
load(file);
%imshow(mat2gray(imageOrig));

% Guaussian blur with standard deviation .66

[x , y] = meshgrid(-4:1:4,-4:1:4); 
bmask = exp(-(x.^2+y.^2)./(2*0.66*0.66));
bmask = bmask./sum(bmask(:));

gblur = imfilter(imageOrig, bmask);
%scaling down
d=2;
subblur = gblur(1:uint32(d):end,1:uint32(d):end);

figure('Name','Gblur 0.66 Image','NumberTitle','off'),
,imshow(mat2gray(subblur));
%% corrupted image
intensityR = max(max(subblur))-min(min(subblur));
corruptim = subblur + (10*intensityR/100)*rand(size(subblur));
   
% figure,imshow(mat2gray(corruptim));
figure('Name','Corrupted Image','NumberTitle','off'),
imshow(mat2gray(corruptim));
% mypatched = corruptim;

%% patching
windowsize = 25;
patchsize = 9;



 mypatched = zeros(size(corruptim));
 for i=1:1:size(corruptim,1)
    for j=1:1:length(corruptim)
 
        if((i-4>0)&&(j-4>0)&&(j+4<=length(corruptim))&&(i+4<=size(corruptim,1)))
        	patchij=corruptim(i-4:i+4,j-4:j+4);
        else
           	patchij = zeros(patchsize);
	         for k=-4:4
	             for l=-4:4
	                 if((i+k>0)&&(j+l>0)&&(i+k<=size(corruptim,1))&&(j+l<=length(corruptim)))
	                     patchij(5+k,5+l) = corruptim(i+k,j+l);
	                 else 
	                 	 patchij(5+k,5+l) =0;
                     end
                 end
             end
        end
        
%          [x , y] = meshgrid(-4:1:4,-4:1:4); 
%         pmask = exp(-(x.^2+y.^2)./(2*0.5*0.5));
%         pmask = pmask./sum(pmask(:));
         
         patchij = imfilter(patchij, bmask);
% 
         patchtemp = zeros(patchsize);
         funcval = 0;
         exptotal = 0;
         for k=-12:12
             for l=-12:12
                 
                 if ((i+k-4>0)&&(j+l-4>0)&&(j+l+4<=length(corruptim))&&(i+k+4<=size(corruptim,1)))
        				patchtemp=corruptim(i+k-4:i+k+4,j+l-4:j+l+4);
        		 else                 
	                  for m=-4:4
	                     for n=-4:4
	                         if((i+k+m>0)&&(j+l+n>0)&&(i+k+m<=size(corruptim,1))&&(j+l+n<=length(corruptim)))
	                             patchtemp(5+m,5+n) = corruptim(i+k+m,j+l+n);
	                         else
	                             patchtemp(5+m,5+n) = 0;
	                         end
	                     end
	                  end
                 end
                
%                  [x , y] = meshgrid(-4:1:4,-4:1:4); 
%                     pmask = exp(-(x.^2+y.^2)./(2*0.5*0.5));
%                     pmask = pmask./sum(pmask(:));
%         
                  patchtemp = imfilter(patchtemp, bmask);
% 
%                   %% two temporary patches generated 
%                  patchtemp = patchij - patchtemp ;
%                
%                   tempe = exp((0-norm(patchtemp)*norm(patchtemp))/(2*sigma*sigma) );
%                   funcval = funcval + tempe * corruptim(i,j); 
%                   exptotal = exptotal + tempe ;
%  
              end
          end
%          
%          mypatched(i,j) = funcval/exptotal ;
         
     end
 end
 
figure('Name','Filtered Image','NumberTitle','off'),
imshow(mat2gray(mypatched));

;end
