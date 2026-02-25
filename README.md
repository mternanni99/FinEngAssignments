# FinEngAssignments
% Assignment_1
%  Group 12, AA2025-2026

%% Pricing parameters 
S0=1;          % underlying price of equity stock  
K=1.1;         % strike price 
r=0.025;       % risk free rate 
TTM=1/3;       % Time to maturity 
sigma=0.213;   % volatility 
d=0.02;        % dividend yield 
nc  = 1e6; % numer of underlying contracts  
Notional  = nc*S0;
flag=1; % flag:  1 call, -1 put

%% Quantity of interest
B= exp(-r*TTM); % Discount

F0=S0*exp(-d*TTM)/B; % FWD price 


%% a) Price the option 

Total_Option_Value =[];  % vector containg the three solutions 

% pricing using closed formula 
pricingMode = [1 2 3]; % 1 ClosedFormula, 2 CRR, 3 Monte Carlo
M=100; % M = simulations for MC, steps for CRR;

for i = 1 : length(pricingMode)
OptionPrice = EuropeanOptionPrice(F0,K,B,TTM,sigma,pricingMode(i),M,flag);
Total_Option_Value = [Total_Option_Value,OptionPrice*nc];
end
fprintf('The total price of the option using closed formula is: %d\n',Total_Option_Value(1));
fprintf('The total price of the option using CRR is: %d\n',Total_Option_Value(2));
fprintf('The total price of the option using MC formula is: %d\n',Total_Option_Value(3));
%Final comment: we can observe that by considering a higher value of M the
%two prices converge to to the price obtained using the closed formula

%% b) Select M according to the criteria mentioned in the class.
precision = 1e-4;  % Bid/Ask is 1bp

[M_CRR,errorCRR]=PlotErrorCRR(F0,K,B,TTM,sigma);
for i = 1 : length(M_CRR)
if (errorCRR(i)< precision)
M_selected = M_CRR(i);
break
end
end
fprintf('The selected number of steps in the CRR model is: %d\n',M_selected);

[M_CM,stdEstim]=PlotErrorMC(F0,K,B,TTM,sigma);
for i = 1 : length(M_CM)
if (stdEstim(i)< precision)
M_selected = M_CM(i);
break
end
end
fprintf('The selected number of steps in the CM model is: %d\n',M_selected);

%Final Comment: in the end we've selected M = 262144, the number is that high
% mainly because of the slow convergence of the MC model, while if we
% consider only the CRR model the error will fall below in a number of
% steps M = 32, so it requires very few steps to reach the 1 bp treshold

%% c) Show that the numerical errors for a call rescale with M approximately as 1/𝑀 for CRR and as 1/√𝑀 for MC.

%Theoretical slopes for comparison
% We anchor these to the first data point for visual alignment
% The Ratio:(M_vector(1) ./ M_vector) decreasing since M_vector increaing 
% the Anchor:CRR_err(1)ensures that the slope passes trough the first
% calculated error point 
CRR_theory = errorCRR(1) * (M_CRR(1) ./ M_CRR);
MC_theory  = stdEstim(1) * (sqrt(M_CM(1)) ./ sqrt(M_CM));

figure(1)
loglog(M_CRR,errorCRR,'-o','LineWidth',1.5)
hold on ;
loglog(M_CM,stdEstim,'-o','LineWidth',1.5)
loglog(M_CRR,CRR_theory,'-.','LineWidth',1.5)
loglog(M_CM,MC_theory,'-.','LineWidth',1.5)
legend('CRR Absolute error','MC standard error','theoretical slope(1/M)','theoretical slope(1/√M)'); 
% last part of the legend added with gemini
grid on;
xlabel('Number of intervals/simulations (M)');
ylabel('Numerical Error');
title('Convergence Analysis: CRR vs Monte Carlo')
 % Final comment:The numerical results confirm that the CRR error rescales approximately as 1/M,
 % evidenced by the blue line following the theoretical slope of -1. The Monte Carlo error rescales as
 % 1/√M, as evidenced by the orange line's slope of -0.5. The oscillations in the CRR error are attributed 
 % to the "Sawtooth" Effect, given by the binomial tree.
 % As the number of steps M increases, the nodes of the tree shift relative to the strike price K,
 % causing the error to fluctuate while maintaining a downward trend.

 %% d) 



