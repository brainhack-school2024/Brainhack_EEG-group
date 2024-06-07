%% Depression Rest
clear all; clc
datalocation='Y:\EEG_Data\PL Cort Depression\EEG Data\Rest Ready\cnt files\';   % Data are here
savelocation='Y:\EEG_Data\PL Cort Depression\EEG Data\Rest Ready\';   % Data are here
locpath=('Y:\Programs\eeglab12_0_2_1b\plugins\dipfit2.2\standard_BESA\standard-10-5-cap385.elp');
cd(datalocation);

Filz=dir('*.cnt');

% Data are 66 chans: 1=64 is EEG, 65 is HEOG, 66 is VEOG.  Some may have 67=EKG
% Ref'd to a site anterior to Fz (AFI cap & Neuroscan amp).   
% 500 Hz
% See .txt files in the folder for the DMDX stimulus presentation code

for subj=1:length(Filz)
    
    disp(['Do Rest --- Subno:    ',Filz(subj).name]); disp(' ');
    Subno=str2num(Filz(subj).name(1:3));
    
    % ----------Load BrainVision data
    EEG = pop_loadcnt(Filz(subj).name, 'dataformat', 'int32', 'keystroke', 'on');
    
    % ----------Get Locs
    EEG = pop_chanedit(EEG, 'lookup', locpath);
    EEG = eeg_checkset( EEG );
    
    % ---------- Get event types
    % 17 starts and finishes it all
    % triggers 1:6 occur every 500 ms
        % 1,3,5 are eyes closed
        % 2,4,6 are eyes open
    % trigges 11:16 occur every 2000 ms
        % 11,13,15 are eyes closed
        % 12,14,16 are eyes open    
    
    % ---------- Save
    save([savelocation,num2str(Subno),'_Depression_REST.mat'],'EEG');
    
    % ---------- Housekeeping
    clear EEG
    
end

BOOM;

%% If you want, you could do some of this to make life easier:

% reref!
EEG = pop_reref(EEG,[find(strcmpi('M1',{EEG.chanlocs.labels})) find(strcmpi('M2',{EEG.chanlocs.labels}))]);

% Remove VEOG & HEOG
EEG.VEOG=squeeze(EEG.data(66,:,:));
EEG.HEOG=squeeze(EEG.data(65,:,:));

% Strip to 60: 33=M1, 43=M2, 60=CB1, 64=CB2, 65=HEOG, 66=VEOG
EEG = pop_select(EEG,'nochannel',[find(strcmpi('CB1',{EEG.chanlocs.labels})) find(strcmpi('CB2',{EEG.chanlocs.labels})) ...
    find(strcmpi('HEOG',{EEG.chanlocs.labels})) find(strcmpi('EKG',{EEG.chanlocs.labels})) find(strcmpi('VEOG',{EEG.chanlocs.labels}))]);

% Remove mean
EEG = pop_rmbase(EEG,[],[]);
