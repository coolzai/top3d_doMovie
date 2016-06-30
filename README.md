# Instruction
1. In `top3d.m` add the following line after line 94
    ```matlab
    save(['xPhys_it',num2str(loop,'%03d'),'.mat'], 'xPhys');
    ```
    It should looks like this:
    ```matlab
    93 % PLOT DENSITIES
    94 if displayflag, clf; display_3D(xPhys); end %#ok<UNRCH>
    95 save(['xPhys_it',num2str(loop,'%03d'),'.mat'], 'xPhys');
    ```
2. Save the `doMovie.m` under the save directory as `top3d.m`
3. Run `doMovie.m`

# doMovie.m
```matlab
function doMovie
% Make a topology optimizatin movie
% By K. Liu

% cutoffL allow smooth animation from 1 to 50 iterations.
% change 0.5 to the desired element density cutoff value
% change smaller 50 to allow faster animation, larger 50 to allow slower animation
cutoffL = linspace(0,0.5,50);

% Do Movie 
i = 0;
while (1)
    i = i + 1;
    filename = ['xPhys_it',num2str(i,'%03d'),'.mat'];
    if exist(filename,'file') == 0
        break;
    end
    load(filename)
    if i <= length(cutoffL)
        cutoff = cutoffL(i);
    else
        cutoff = max(cutoffL);
    end
    
    plot_3d(xPhys, cutoff, 1, [0.7 0.7 0.7]);
    drawnow
    mov(i) = getframe(gcf); %#ok<AGROW>
end

movie2avi(mov, 'myTopopt.avi'); %#ok<MOVIE2>
end

% DISPLAY 3D TOPOLOGY (ISO-VIEW)
% Special thanks to Tomas Zegard (UIUC) for quick plot function
% Code snippets from Top3d (top3dapp.com)
% Top3d - An efficient 3D topology optimization program written in MATLAB
% By K. Liu and A. Tovar
% http://www.top3dapp.com
function [p] = plot_3d(x, cutoff, Alpha, fcolor)

if nargin < 4, fcolor = 'r'; end

[nely,nelx,nelz] = size(x);
aux = zeros(nely+2,nelx+2,nelz+2);
aux(2:end-1,2:end-1,2:end-1) = x;

cla, hold on, view(30,30), rotate3d on, axis equal, axis([0 nelx 0 nelz 0 nely]), box
set(gca,'YDir','reverse','ZDir','reverse','ZtickLabel',flipud(get(gca,'Ztick')'));
% set(gcf,'MenuBar','none','ToolBar','none')
[X,Y,Z] = meshgrid(0:nelx+1,0:nely+1,0:nelz+1);
p = patch(isosurface(X-0.5,Z-0.5,Y-0.5,aux,cutoff),...
    'FaceColor',fcolor,'EdgeColor','none','FaceAlpha',Alpha);
camlight, lighting gouraud;
axis off; box off; set(gcf, 'color', [1 1 1])
drawnow
end
```