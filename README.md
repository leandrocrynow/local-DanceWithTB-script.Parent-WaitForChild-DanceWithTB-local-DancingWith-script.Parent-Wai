```lua
local DanceWithTB = script.Parent:WaitForChild("DanceWithTB");
local DancingWith = script.Parent:WaitForChild("DancingWith");
local CurrentlyWith = DancingWith:WaitForChild("CurrentlyWith");
local Cancel = DancingWith:WaitForChild("Cancel");
local u1 = nil;
local LocalPlayer = game.Players.LocalPlayer;
local DanceWithServer = game.ReplicatedStorage:WaitForChild("DanceWithServer");
local TextBox = DanceWithTB:WaitForChild("TextBox");
local Prediction = DanceWithTB:WaitForChild("Prediction");
local BG = script.Parent:WaitForChild("BG");
local DanceWithCover = script.Parent:WaitForChild("DanceWithCover");
local u8 = nil;
local u9 = nil;
local u10 = nil;
local u11 = nil;
local u12 = nil;
local function u13(p1, p2)
	pcall(function()
		local v5 = nil;
		for v6, v7 in pairs(p2.Character:WaitForChild("Humanoid"):WaitForChild("Animator"):GetPlayingAnimationTracks()) do
			if v7.Priority == Enum.AnimationPriority.Action then
				v5 = v7;
			end;
		end;
		for v8, v9 in pairs(p1.Character:WaitForChild("Humanoid"):WaitForChild("Animator"):GetPlayingAnimationTracks()) do
			if v9.Priority == Enum.AnimationPriority.Action then
				v9.TimePosition = v5.TimePosition;
			end;
		end;
	end);
end;
DanceWithServer.OnClientEvent:Connect(function(p3, p4, p5)
	if p3 == "Sync" then
		u13(p4, p5);
		delay(1, function()
			u13(p4, p5);
		end);
	end;
end);
local u14 = nil;
TextBox:GetPropertyChangedSignal("Text"):Connect(function()
	local v10 = false;
	if TextBox.Text ~= "" and TextBox.Text ~= " " then
		for v11, v12 in pairs(game.Players:GetPlayers()) do
			if v12 ~= LocalPlayer and TextBox.Text:lower() == v12.Name:lower():sub(1, #TextBox.Text) then
				Prediction.Text = TextBox.Text .. v12.Name:sub(#TextBox.Text + 1);
				v10 = true;
				u14 = v12;
			end;
		end;
	end;
	if not v10 then
		u14 = nil;
		Prediction.Text = "";
	end;
end);
TextBox.Focused:Connect(function()
	TextBox.TextXAlignment = Enum.TextXAlignment.Left;
	Prediction.TextXAlignment = Enum.TextXAlignment.Left;
	TextBox.PlaceholderText = "Type player name";
end);
local function u15(p6, p7)
	for v13, v14 in pairs(p6.Character:WaitForChild("Humanoid"):WaitForChild("Animator"):GetPlayingAnimationTracks()) do
		if v14.Priority == Enum.AnimationPriority.Action then
			if p7 and v14.Name == "SyncedAnimation" then
				return v14;
			end;
			if not p7 then
				return v14;
			end;
		end;
	end;
end;
local function u16()
	if u1 ~= nil then
		for v15, v16 in pairs((LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()):WaitForChild("Humanoid"):WaitForChild("Animator"):GetPlayingAnimationTracks()) do
			if v16.Priority == Enum.AnimationPriority.Action and v16.Name == "SyncedAnimation" then
				v16:Stop();
				v16:Destroy();
			end;
		end;
		DanceWithServer:FireServer("RemoveSync", "");
		TextBox.Text = "";
		Prediction.Text = "";
		DanceWithTB.Visible = true;
		DancingWith.Visible = false;
		BG.Visible = true;
		DanceWithCover.Visible = false;
		u1 = nil;
		u8:Disconnect();
		u8 = nil;
		u9:Disconnect();
		u9 = nil;
		u10:Disconnect();
		u10 = nil;
		u11:Disconnect();
		u11 = nil;
		u12:Disconnect();
		u12 = nil;
	end;
end;
TextBox.FocusLost:Connect(function()
	TextBox.TextXAlignment = Enum.TextXAlignment.Center;
	Prediction.TextXAlignment = Enum.TextXAlignment.Center;
	TextBox.PlaceholderText = "Dance With";
	if u14 == nil then
		if TextBox.Text ~= "" and TextBox.Text ~= " " then
			TextBox.Text = "Invalid player";
			wait(1);
			TextBox.Text = "";
		end;
		return;
	end;
	if u14:FindFirstChild("SyncedWith") then
		local Value = u14.SyncedWith.Value;
		if Value == LocalPlayer then
			TextBox.Text = "Cannot sync yourself";
			wait(1);
			TextBox.Text = "";
			return;
		end;
		u14 = Value;
	end;
	if not u15(u14, false) then
		TextBox.Text = "Player not dancing";
		wait(1);
		TextBox.Text = "";
		return;
	end;
	DanceWithServer:FireServer("DanceWith", u14.Name);
	u1 = u14;
	local v18 = u14.Character or u14.CharacterAdded:Wait();
	local Humanoid = v18:WaitForChild("Humanoid");
	local Animator = v18:WaitForChild("Humanoid"):WaitForChild("Animator");
	local Humanoid = (LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()):WaitForChild("Humanoid");
	local Animator = Humanoid:WaitForChild("Animator");
	for v23, v24 in pairs(Animator:GetPlayingAnimationTracks()) do
		if v24.Priority == Enum.AnimationPriority.Action then
			v24:Stop();
			v24:Destroy();
		end;
	end;
	local v25 = nil;
	for v26, v27 in pairs(Animator:GetPlayingAnimationTracks()) do
		if v27.Priority == Enum.AnimationPriority.Action then
			v25 = v27;
		end;
	end;
	local v28 = Animator:LoadAnimation(v25.Animation);
	v28.Name = "SyncedAnimation";
	v28:Play();
	u8 = game.Players.PlayerRemoving:Connect(function(p8)
		if p8 == u1 then
			u16();
		end;
	end);
	u10 = Humanoid.Died:Connect(function()
		u16();
	end);
	u11 = Humanoid.Died:Connect(function()
		u16();
	end);
	u12 = u14.ChildAdded:Connect(function(p9)
		if p9:IsA("ObjectValue") and p9.Name == "SyncedWith" then
			u16();
		end;
	end);
	v25.Stopped:Connect(function()
		v28:Stop();
		v28:Destroy();
	end);
	u9 = Animator.AnimationPlayed:Connect(function(p10)
		if p10.Priority == Enum.AnimationPriority.Action then
			local v29 = u15(LocalPlayer, true);
			if v29 then
				v29:Stop();
				v29:Destroy();
			end;
			local v30 = (LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()):WaitForChild("Humanoid"):WaitForChild("Animator"):LoadAnimation(p10.Animation);
			v30.Name = "SyncedAnimation";
			v30:Play();
			v30.TimePosition = p10.TimePosition;
			p10.Stopped:Connect(function()
				v30:Stop();
				v30:Destroy();
			end);
			delay(0.5, function()
				u13(LocalPlayer, u1);
			end);
		end;
	end);
	CurrentlyWith.Text = "Sync: " .. u1.Name;
	DanceWithTB.Visible = false;
	DancingWith.Visible = true;
	BG.Visible = false;
	DanceWithCover.Visible = true;
end);
Cancel.MouseButton1Click:Connect(function()
	u16();
end);
while wait(2.5) do
	for v31, v32 in pairs(game.Players:GetPlayers()) do
		if v32:FindFirstChild("SyncedWith") then
			u13(v32, v32.SyncedWith.Value);
		end;
	end;
end;


```
